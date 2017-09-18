pipeline {
    agent {
        node { label 'master' }
    }

    stages {
        stage('Download') {
            steps {
                // Delete old files
                sh 'rm -rf .[^.] .??* *'

                // Checkout the git repository and refspec pointed to by jenkins
                checkout scm
            }
        }

        // Configure the software
        stage('Configure') {
            steps {
                script {
                    sh "bundle install --path vendor/bundle"
                }
            }
        }

        stage("Build") {
            steps {
                sh "bundle exec jekyll build"
            }
        }

        // Dont't run tests untill we have moved the IRC logs
        // because the build server can't reach it and fails for now.
        // stage("Test") {
        //    steps {
        //        sh "bundle exec htmlproofer _site"
        //    }
        // }

        // This stage assumes that /var/www is mounted/linked/whatever to
        // where one wants to deploy the site on the node that runs this job
        stage('Deploy') {
            when {
                branch 'master'
            }

            steps {
                sh 'rm -rf /var/www/pelux.io'
                sh 'cp -R _site /var/www/'
                sh 'mv /var/www/_site /var/www/pelux.io'
            }
        }

        stage('Archive') {
            steps {
                // Store the artifacts of the entire build
                archive "_site/**/*"
            }
        }
    }
}