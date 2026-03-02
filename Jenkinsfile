pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                dir('Password Protection') {  // go to the folder with src and test
                    sh '''
                        echo "Building Java project..."
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
                        echo "Running JUnit tests..."
                        
                        # Remove old/corrupt JUnit JAR if exists
                        rm -f junit-platform-console-standalone.jar

                        # Download fresh JUnit JAR
                        curl -L -o junit-platform-console-standalone.jar \
                        https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar

                        # Verify JAR
                        if [ ! -s junit-platform-console-standalone.jar ]; then
                            echo "ERROR: JUnit JAR download failed!"
                            exit 1
                        fi

                        # Compile test files
                        mkdir -p test-build
                        find test -name "*.java" > test-sources.txt
                        javac -cp junit-platform-console-standalone.jar:build -d test-build @test-sources.txt

                        # Run JUnit tests
                        java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('Password Protection') {
                    sh '''
                        echo "Packaging application..."
                        jar cf FileEncrypter.jar -C build .
                        echo "Deployment successful"
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
