node {
    try {
        stage('Build') {
            dir('Password Protection') {
                sh '''
                    echo "Building Java project..."
                    mkdir -p build
                    javac -d build src/*.java
                    echo "Build successful"
                '''
            }
        }

        stage('Test') {
            dir('Password Protection') {
                sh '''
                    echo "Running JUnit tests..."
                    
                    # Remove old/corrupt JUnit JAR
                    rm -f junit-platform-console-standalone.jar
                    
                    # Download fresh JUnit
                    curl -L -o junit-platform-console-standalone.jar \
                    https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
                    
                    # Compile test files
                    mkdir -p test-build
                    javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java
                    
                    # Run tests
                    java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
                    
                    echo "JUnit tests executed successfully"
                '''
            }
        }

        stage('Deploy') {
            dir('Password Protection') {
                sh '''
                    echo "Packaging File-Encrypter..."
                    jar cf FileEncrypter.jar -C build .
                    echo "Deployment successful - Artifact ready"
                '''
            }
        }

        echo "Pipeline executed successfully!"
    } catch (Exception e) {
        echo "Pipeline failed!"
        throw e
    }
}
