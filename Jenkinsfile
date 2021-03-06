pipeline {
	agent {
		docker {
			image 'ansible/ansible:default'
			label 'slave-builder'
			args '-u 0:0 -v /home/jenkins/.ssh:/root/.ssh'
		} //docker
	} //agent
	stages {
		stage("Set up") {
			steps {
				sh """
					pip install --upgrade pip
					pip install ansible 
					apt-get install -y zip
				"""
			} //steps
		} //stage
		stage("Build") {
			steps {
				sh """
					ansible-playbook -i inventory jenkins-slave-setup.yml
				"""
			} //steps
		} //stage
                stage("Test") {
                        steps {
                                sh """
                                        ansible jenkins-slaves -i inventory -m shell -a "ls /home/jenkins/jenkins_slave"
                                        ansible jenkins-slaves -i inventory -m shell -a "id jenkins"
                                        ansible jenkins-slaves -i inventory -m shell -a "cat /home/jenkins/.ssh/authorized_keys"
                                """
                        } //steps
                } //stage
                stage("Deploy") {
                        steps {
                                sh """
                                        zip jenkins-slave-setup.zip jenkins-slave-setup
					ansible jenkins-slaves -i inventory -m shell -a "mkdir -p /var/www/html/my-repository"
					scp jenkins-slave-setup.zip root@devx-web001:/var/www/html/my-repository
                                """
                        } //steps
                } //stage

	} //stages
} //pipeline
