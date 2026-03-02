node {

    try {

        stage('Build') {
            sh '''
            echo "===== BUILD STAGE ====="
            echo "Listing workspace contents:"
            ls

            cd "Password Protection"

            echo "Cleaning old build..."
            rm -rf build
            mkdir -p build

            echo "Compiling Java source files..."
            javac -d build src/*.java

            echo "Build successful"
            '''
        }

        stage('Test') {
            sh '''
            echo "===== TEST STAGE ====="
            cd "Password Protection"

            echo "Removing old JUnit jar if exists..."
            rm -f junit-platform-console-standalone.jar

            echo "Downloading JUnit..."
            curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar

            if [ ! -f junit-platform-console-standalone.jar ]; then
                echo "JUnit download failed!"
                exit 1
            fi

            if [ -d "test" ]; then
                echo "Compiling test files..."
                rm -rf test-build
                mkdir -p test-build

                javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java

                echo "Running JUnit tests..."
                java -jar junit-platform-console-standalone.jar \
                --class-path build:test-build \
                --scan-class-path
            else
                echo "No test directory found. Skipping tests."
            fi

            echo "Test stage completed"
            '''
        }

        stage('Deploy') {
            sh '''
            echo "===== DEPLOY STAGE ====="
            cd "Password Protection"

            echo "Packaging JAR file..."
            jar cf FileEncrypter.jar -C build .

            if [ -f FileEncrypter.jar ]; then
                echo "Deployment successful - Artifact ready"
            else
                echo "Deployment failed!"
                exit 1
            fi
            '''
        }

        echo "===== PIPELINE EXECUTED SUCCESSFULLY ====="

    } catch (Exception e) {

        echo "===== PIPELINE FAILED ====="
        throw e
    }
}
