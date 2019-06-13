
pipeline {
	agent any
	environment {
		gradleHome = tool 'mygradle-4.10'     
	}
	stages {
		stage('更新项目代码') {
			steps {
				checkout([$class: 'GitSCM', branches: [[name: '*/Cheny'], [name: '*/dev']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '9e4bdd05-b24f-4ca1-b173-fbc24d4c93ec', url: 'http://192.168.1.15:8080/tfs/Mobile.Dev/Android/_git/EhaiCarService/']]])
			}
		}
		stage('构建应用') {
			steps {
				script {
					if (isUnix()) {
						sh "'${gradleHome}/bin/gradle' -v"
					} else {
						bat "${gradleHome}\\bin\\gradle clean jacocoTestReport buildEhaiCarServiceApk${params.Environment} -stacktrace -debug -PIS_JENKINS=true -Pandroid.buildCacheDir=D:/android-build-cache"
					}	
				}
			}
		}
		stage('生成 APK 二维码') {
			steps {  	
				bat "ren .\\app\\build\\outputs\\apk\\${params.Environment}\\*.apk ehaicarservice_${BUILD_TIMESTAMP}.apk"
				sshPublisher(publishers: [sshPublisherDesc(configName: '192.168.5.30-SSH', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/jenkins_build/ EhaiCarServiceAPK', remoteDirectorySDF: false, removePrefix: '/app/build/outputs/apk/', sourceFiles: "/app/build/outputs/apk/${params.Environment}/*.apk")], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
				bat "java -jar D:\\test\\qr.jar url=http://192.168.5.30:80/automationtest/script/ EhaiCarServiceAPK/${params.Environment}/ehaicarservice_${BUILD_TIMESTAMP}.apk image=ehaicarservice_${BUILD_TIMESTAMP}.jpg save=./"
				sshPublisher(publishers: [sshPublisherDesc(configName: '192.168.5.30-SSH', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: "/jenkins_build/ EhaiCarServiceAPK/${params.Environment}/", remoteDirectorySDF: false, removePrefix: '', sourceFiles: "ehaicarservice_${BUILD_TIMESTAMP}.jpg")], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
				script {
					currentBuild.description = "<img src='http://192.168.5.30:80/automationtest/script/ EhaiCarServiceAPK/${params.Environment}/ehaicarservice_${BUILD_TIMESTAMP}.jpg' alt='二维码${BUILD_TIMESTAMP}' width='200' height='200' /><a href='http://192.168.5.30:80/automationtest/script/ EhaiCarServiceAPK/${params.Environment}/ehaicarservice_${BUILD_TIMESTAMP}.jpg'>${params.Environment}_${BUILD_TIMESTAMP}</a>"
					
					if (params.Environment != 'release') {
						bat "ren .\\app\\build\\outputs\\apk\\${params.Environment}\\*.apk ehaicarservice.apk"
						sshPublisher(publishers: [sshPublisherDesc(configName: '192.168.5.30-SSH', transfers: [sshTransfer(cleanRemote: true, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/jenkins_build/ EhaiCarServiceAPK/latest/', remoteDirectorySDF: false, removePrefix: "/app/build/outputs/apk/${params.Environment}", sourceFiles: "/app/build/outputs/apk/${params.Environment}/*.apk")], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
					}
				}
			}	
		}
	}
}
