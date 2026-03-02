pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                dir('Password Protection') {
                    sh '''
                        echo "Building Java project..."
                        ls
                        mkdir -p build
                        javac -d build src/*.java
                        echo "Build successful"
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                dir('Password Protection') {
                    sh '''
                        echo "Running JUnit tests for File-Encrypter..."

                        # Download JUnit if missing or empty
                        if [ ! -f junit-platform-console-standalone.jar ] || [ ! -s junit-platform-console-standalone.jar ]; then
                            echo "Downloading JUnit..."
                            curl -L -o junit-platform-console-standalone.jar \
                            https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
                        fi

                        # Verify download
                        if [ ! -s junit-platform-console-standalone.jar ]; then
                            echo "ERROR: JUnit download failed!"
                            exit 1
                        fi

                        # Compile test files
                        mkdir -p test-build
                        find test -name "*.java" > test-sources.txt
                        javac -cp junit-platform-console-standalone.jar:build -d test-build @test-sources.txt

                        # Run tests
                        java -jar junit-platform-console-standalone.jar \
                        --class-path build:test-build \
                        --scan-class-path

                        echo "JUnit tests executed successfully"
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('Password Protection') {
                    sh '''
                        echo "Deploying (Packaging) File-Encrypter Application..."
                        mkdir -p dist
                        jar cf dist/FileEncrypter.jar -C build .
                        echo "Deployment successful - Artifact ready in dist/FileEncrypter.jar"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
