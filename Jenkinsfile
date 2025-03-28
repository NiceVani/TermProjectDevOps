pipeline {
    agent any

    environment {
        NETLIFY_AUTH_TOKEN = credentials('netlify-auth-token')
        NETLIFY_SITE_ID = credentials('netlify-site-id')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                script {
                    echo "🏗️ Building the project..."
                    sh '''
                    npm install
                    npx react-scripts build'''
                }
            }
            post {
                success {
                    echo "✅ Build Successful! 🎉"
                }
                failure {
                    echo "❌ Build Failed! Check logs for details."
                }
            }
        }

        // Run tests (if applicable)
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                script {
                    echo "🔬 Running tests..."
                    sh 'npm test'  // ปรับคำสั่งให้เป็นคำสั่งที่ใช้ทดสอบโปรเจคของคุณ
                }
            }
            post {
                success {
                    echo "✅ Test Successful! 🎉"
                }
                failure {
                    echo "❌ Test Failed! Check logs for details."
                }
            }
        }

        // Deploy to Netlify
        stage('Install Puppeteer and Chromium') {
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
        }
    }
    steps {
        script {
            echo "📦 Installing Chromium and Puppeteer..."
            sh '''
            apk update
            apk add --no-cache chromium
            npm install puppeteer
            node -e "const puppeteer = require('puppeteer'); (async () => { const browser = await puppeteer.launch(); const page = await browser.newPage(); await page.goto('https://nicevanitermproject.netlify.app'); await page.screenshot({path: 'screenshot.png'}); await browser.close(); })();"
            '''
        }
    }


            post {
                success {
                    echo "✅ Deployment Successful! 🎉"
                }
                failure {
                    echo "❌ Deployment Failed! Check logs for details."
                }
            }
        }

        // Post deploy actions, e.g., notify Slack, send emails, etc.
        stage('Post Deploy') {
            agent any
            steps {
                script {
                    echo "🔍 Monitoring server resources during the test..."
            
                    // Run resource monitoring commands and save output
                    try {
                        sh '''
                            echo "Top 10 processes by memory usage:" > resource_report.txt
                            ps aux --sort=-%mem | head -n 10 >> resource_report.txt
                            
                            echo "\nMemory usage:" >> resource_report.txt
                            free -h >> resource_report.txt
                            
                            echo "\nSystem performance stats (vmstat):" >> resource_report.txt
                            vmstat 1 5 >> resource_report.txt
                        '''
                    } catch (e) {
                        echo "Error monitoring server resources: ${e}"
                    }
                }
            }
            post {
                success {
                    echo "✅ Resource monitoring completed successfully! Here are the results:"
                    sh 'cat resource_report.txt'  // แสดงข้อมูลที่บันทึกไว้
                }
                failure {
                    echo "❌ Resource monitoring encountered an error!"
                }
            }
        }
    }

}
