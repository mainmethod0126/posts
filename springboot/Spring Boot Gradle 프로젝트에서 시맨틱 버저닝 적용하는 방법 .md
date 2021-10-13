Spring Boot Gradle 프로젝트를 진행하면서 시맨틱 버전을 적용할 경우가 있어서 이에 대한 기록을 남깁니다.

</br>

# 시맨틱 버저닝이란?(Semantic Versioning)

시맨틱 버저닝은

```
1.0.0-alpha+test // major.minor.patch-prerelease+buildMetaData
```

와 같은 구조를 띄는 버전 기법을 말합니다.
좀 더 자세한 정보는 https://velog.io/@iamjoo/Semantic-Versioning%EC%9D%B4%EB%9E%80 를 참고 부탁드리겠습니다.

</br>

# Gradle을 이용한 시맨틱 버저닝 적용

우리의 Gradle을 Groovy 라는 프로그래밍 언어를 이용하여 마치 코딩을 하듯이 조작할 수 있습니다.
이것이 maven과의 큰 차이점이라 볼 수 있을 것 같은대요
저는 이 특성을 이용하여 버전 정보를 **version.json 파일**로 저장하고 **gradlew build** 명령어를 실행 시 **version.json 파일**을 읽고, 쓰는 작업을 통하여 version을 관리하도록 하였습니다.

</br>

## 기존 버전 조회
먼저 기존에 버전 정보가 있다면 그 정보를 읽어와야합니다.
버전 정보는 version.json에서 읽을 수 있으며 파일이 없다면 생성합니다.

~~~java
build {
    // 기존 버전 정보를 가져옵니다.
	File versionJosnFile = new File("version.json");
	if (!versionJosnFile.exists()) {
		if (versionJosnFile.createNewFile()) {
            versionJosnFile.write(
				 """ {
					"major": 1,
					"minor": 0,
					"patch": 0
				} """
			);
        } else {
            throw new GradleException("create failed version.json");
        }
    }

    // gradle 가장 상단에 import groovy.json.JsonSlurper 가 명시되어있어야 JsonSlurper를 사용할 수 있습니다
    def json = new JsonSlurper().parseText(versionJosnFile.text);
	String oldMajor = json.major;
	String oldMinor = json.minor;
	String oldPatch = json.patch;
}
~~~

</br>

## major version

major version 같은 경우 프로그램이 정말 확 바뀌었으때 마치 스타크래프트1에서 스타크래프트2 가 나왔을 때 같이 뿌리는 같지만 모든게 바뀌었을 경우 증가되는 버전 항목입니다.

</br>

major 버전을 증가시킬지 기존 그대로 유지할지는 사용자의 입력을 받겠습니다.
먼저 공통으로 쓰일 사용자 입력 함수를 생성합니다.

</br>

### 사용자 입력을 받는 공통 함수
~~~java
String inputVersionIncrement(String versionName, String oldVersion) {
	println("Enter 'Y' if you want to increment the "+ versionName +" version, otherwise press any key");
	String inputFlag = System.in.newReader().readLine();
	String nextVersion;
    if (inputFlag == "Y" || inputFlag == "y") {
		nextVersion = String.valueOf(Integer.valueOf(oldVersion) + 1);
		println("version changed");
		println(versionName + " version : " + oldVersion + " ---> " + nextVersion);
	} else {
		nextVersion = oldVersion;
		println("version not change");
		println(versionName + " version : " + oldVersion);
	}
	return nextVersion;
}
~~~

### major 버전 증가 여부 판단
```java
String inputVersionIncrement(String versionName, String oldVersion) {
	println("Enter 'Y' if you want to increment the "+ versionName +" version, otherwise press any key");
	String inputFlag = System.in.newReader().readLine();
	String nextVersion;
    if (inputFlag == "Y" || inputFlag == "y") {
		nextVersion = String.valueOf(Integer.valueOf(oldVersion) + 1);
		println("version changed");
		println(versionName + " version : " + oldVersion + " ---> " + nextVersion);
	} else {
		nextVersion = oldVersion;
		println("version not change");
		println(versionName + " version : " + oldVersion);
	}
	return nextVersion;
}

build {
    // 기존 버전 정보를 가져옵니다.
	File versionJosnFile = new File("version.json");
	if (!versionJosnFile.exists()) {
		if (versionJosnFile.createNewFile()) {
            versionJosnFile.write(
				 """ {
					"major": 1,
					"minor": 0,
					"patch": 0
				} """
			);
        } else {
            throw new GradleException("create failed version.json");
        }
    }

    // gradle 가장 상단에 import groovy.json.JsonSlurper 가 명시되어있어야 JsonSlurper를 사용할 수 있습니다
    def json = new JsonSlurper().parseText(versionJosnFile.text);
    String oldMajor = json.major;
    String oldMinor = json.minor;
    String oldPatch = json.patch;

    String major = inputVersionIncrement("major", oldMajor);
}
```

</br>

## minor version

minor version 같은 경우 모든게 바뀌지는 않았지만 부분적으로 바뀐게 많아 이전 버전과 확실한 구분이 존재할 때 증가되는 버전입니다. 
이 또한 사용자의 입력을 받겠습니다.

```java
String inputVersionIncrement(String versionName, String oldVersion) {
	println("Enter 'Y' if you want to increment the "+ versionName +" version, otherwise press any key");
	String inputFlag = System.in.newReader().readLine();
	String nextVersion;
    if (inputFlag == "Y" || inputFlag == "y") {
		nextVersion = String.valueOf(Integer.valueOf(oldVersion) + 1);
		println("version changed");
		println(versionName + " version : " + oldVersion + " ---> " + nextVersion);
	} else {
		nextVersion = oldVersion;
		println("version not change");
		println(versionName + " version : " + oldVersion);
	}
	return nextVersion;
}

build {
    // 기존 버전 정보를 가져옵니다.
	File versionJosnFile = new File("version.json");
	if (!versionJosnFile.exists()) {
		if (versionJosnFile.createNewFile()) {
            versionJosnFile.write(
				 """ {
					"major": 1,
					"minor": 0,
					"patch": 0
				} """
			);
        } else {
            throw new GradleException("create failed version.json");
        }
    }

    // gradle 가장 상단에 import groovy.json.JsonSlurper 가 명시되어있어야 JsonSlurper를 사용할 수 있습니다
    def json = new JsonSlurper().parseText(versionJosnFile.text);
    String oldMajor = json.major;
    String oldMinor = json.minor;
    String oldPatch = json.patch;

    String major = inputVersionIncrement("major", oldMajor);
    String minor = inputVersionIncrement("minor", oldMinor);
}
```

</br>

## patch version

patch version 같은 경우 버그 수정 또는 간단한 수정이 이루어졌을 경우 증가되는 버전이며 수시로 바뀔 수 있습니다.
이 또한 사용자의 입력을 받겠습니다.

```java
String inputVersionIncrement(String versionName, String oldVersion) {
	println("Enter 'Y' if you want to increment the "+ versionName +" version, otherwise press any key");
	String inputFlag = System.in.newReader().readLine();
	String nextVersion;
    if (inputFlag == "Y" || inputFlag == "y") {
		nextVersion = String.valueOf(Integer.valueOf(oldVersion) + 1);
		println("version changed");
		println(versionName + " version : " + oldVersion + " ---> " + nextVersion);
	} else {
		nextVersion = oldVersion;
		println("version not change");
		println(versionName + " version : " + oldVersion);
	}
	return nextVersion;
}

build {
    // 기존 버전 정보를 가져옵니다.
	File versionJosnFile = new File("version.json");
	if (!versionJosnFile.exists()) {
		if (versionJosnFile.createNewFile()) {
            versionJosnFile.write(
				 """ {
					"major": 1,
					"minor": 0,
					"patch": 0
				} """
			);
        } else {
            throw new GradleException("create failed version.json");
        }
    }

    // gradle 가장 상단에 import groovy.json.JsonSlurper 가 명시되어있어야 JsonSlurper를 사용할 수 있습니다
    def json = new JsonSlurper().parseText(versionJosnFile.text);
    String oldMajor = json.major;
    String oldMinor = json.minor;
    String oldPatch = json.patch;

    String major = inputVersionIncrement("major", oldMajor);
    String minor = inputVersionIncrement("minor", oldMinor);
    String patch = inputVersionIncrement("patch", oldPatch);
}
```

</br>

## pre-release version

pre-release version은 정식 release 되기전 테스트 단계의 버전들을 명시합니다.
우리가 게임에서 많이 들어보는 알파테스트, 베타테스트 가 이 버전이 될 수 있습니다.

</br>

major,minor,patch 와 같이 단순 숫자의 증가가 아닌 입력받은 텍스트 전체를 사용하겠습니다.
만약 사용자 입력이 없을 경우 기본값은 'alpha'입니다

</br>

```java
String inputVersionIncrement(String versionName, String oldVersion) {
	println("Enter 'Y' if you want to increment the "+ versionName +" version, otherwise press any key");
	String inputFlag = System.in.newReader().readLine();
	String nextVersion;
    if (inputFlag == "Y" || inputFlag == "y") {
		nextVersion = String.valueOf(Integer.valueOf(oldVersion) + 1);
		println("version changed");
		println(versionName + " version : " + oldVersion + " ---> " + nextVersion);
	} else {
		nextVersion = oldVersion;
		println("version not change");
		println(versionName + " version : " + oldVersion);
	}
	return nextVersion;
}

build {
    // 기존 버전 정보를 가져옵니다.
	File versionJosnFile = new File("version.json");
	if (!versionJosnFile.exists()) {
		if (versionJosnFile.createNewFile()) {
            versionJosnFile.write(
				 """ {
					"major": 1,
					"minor": 0,
					"patch": 0
				} """
			);
        } else {
            throw new GradleException("create failed version.json");
        }
    }

    // gradle 가장 상단에 import groovy.json.JsonSlurper 가 명시되어있어야 JsonSlurper를 사용할 수 있습니다
    def json = new JsonSlurper().parseText(versionJosnFile.text);
    String oldMajor = json.major;
    String oldMinor = json.minor;
    String oldPatch = json.patch;

    String major = inputVersionIncrement("major", oldMajor);
    String minor = inputVersionIncrement("minor", oldMinor);
    String patch = inputVersionIncrement("patch", oldPatch);

    println("Please enter prereleaseVersion Default is \'alpha\'");
    String prereleaseVersion = System.in.newReader().readLine();
    if (prereleaseVersion != "") {
        println("prerelease version : " + prereleaseVersion);
    } else {
        prereleaseVersion = "alpha";
    }
    println("prerelease version : " + prereleaseVersion);
    prereleaseVersion = "-" + prereleaseVersion;
}
```

</br>

## build meta data

제가 알기로는 이 항목은 일종의 description 구간입니다.
버전에대한 기타 정보들을 기입할 수 있습니다.
이 또한 사용자의 입력을 받겠습니다.

```java
String inputVersionIncrement(String versionName, String oldVersion) {
	println("Enter 'Y' if you want to increment the "+ versionName +" version, otherwise press any key");
	String inputFlag = System.in.newReader().readLine();
	String nextVersion;
    if (inputFlag == "Y" || inputFlag == "y") {
		nextVersion = String.valueOf(Integer.valueOf(oldVersion) + 1);
		println("version changed");
		println(versionName + " version : " + oldVersion + " ---> " + nextVersion);
	} else {
		nextVersion = oldVersion;
		println("version not change");
		println(versionName + " version : " + oldVersion);
	}
	return nextVersion;
}

build {
    // 기존 버전 정보를 가져옵니다.
	File versionJosnFile = new File("version.json");
	if (!versionJosnFile.exists()) {
		if (versionJosnFile.createNewFile()) {
            versionJosnFile.write(
				 """ {
					"major": 1,
					"minor": 0,
					"patch": 0
				} """
			);
        } else {
            throw new GradleException("create failed version.json");
        }
    }

    // gradle 가장 상단에 import groovy.json.JsonSlurper 가 명시되어있어야 JsonSlurper를 사용할 수 있습니다
    def json = new JsonSlurper().parseText(versionJosnFile.text);
    String oldMajor = json.major;
    String oldMinor = json.minor;
    String oldPatch = json.patch;

    String major = inputVersionIncrement("major", oldMajor);
    String minor = inputVersionIncrement("minor", oldMinor);
    String patch = inputVersionIncrement("patch", oldPatch);

    println("Please enter prereleaseVersion Default is \'alpha\'");
    String prereleaseVersion = System.in.newReader().readLine();
    if (prereleaseVersion != "") {
        println("prerelease version : " + prereleaseVersion);
    } else {
        prereleaseVersion = "alpha";
    }
    println("prerelease version : " + prereleaseVersion);
    prereleaseVersion = "-" + prereleaseVersion;

    println("Enter buildMetadata Default is empty");
    String buildMetadata = System.in.newReader().readLine();
    if (buildMetadata != "") {
        buildMetadata = "+" + buildMetadata;
    }
    println("buildMetadata : " + buildMetadata);
}
```

</br>

## build 결과물 이름 지정 및 저장 위치 변경

이제 만들어질 결과물에 version정보를 붙여서 이름을 만듭니다.
그 다음 build 결과물을 내가 원하는 곳에 보기좋게 분리하는게 좋을 것 이라고 판단하여 빌드 날짜와 버전으로 폴더를 분리하여 위치하도록 합니다.

```java
String inputVersionIncrement(String versionName, String oldVersion) {
	println("Enter 'Y' if you want to increment the "+ versionName +" version, otherwise press any key");
	String inputFlag = System.in.newReader().readLine();
	String nextVersion;
    if (inputFlag == "Y" || inputFlag == "y") {
		nextVersion = String.valueOf(Integer.valueOf(oldVersion) + 1);
		println("version changed");
		println(versionName + " version : " + oldVersion + " ---> " + nextVersion);
	} else {
		nextVersion = oldVersion;
		println("version not change");
		println(versionName + " version : " + oldVersion);
	}
	return nextVersion;
}

build {
    // 기존 버전 정보를 가져옵니다.
	File versionJosnFile = new File("version.json");
	if (!versionJosnFile.exists()) {
		if (versionJosnFile.createNewFile()) {
            versionJosnFile.write(
				 """ {
					"major": 1,
					"minor": 0,
					"patch": 0
				} """
			);
        } else {
            throw new GradleException("create failed version.json");
        }
    }

    // gradle 가장 상단에 import groovy.json.JsonSlurper 가 명시되어있어야 JsonSlurper를 사용할 수 있습니다
    def json = new JsonSlurper().parseText(versionJosnFile.text);
    String oldMajor = json.major;
    String oldMinor = json.minor;
    String oldPatch = json.patch;

    String major = inputVersionIncrement("major", oldMajor);
    String minor = inputVersionIncrement("minor", oldMinor);
    String patch = inputVersionIncrement("patch", oldPatch);

    println("Please enter prereleaseVersion Default is \'alpha\'");
    String prereleaseVersion = System.in.newReader().readLine();
    if (prereleaseVersion != "") {
        println("prerelease version : " + prereleaseVersion);
    } else {
        prereleaseVersion = "alpha";
    }
    println("prerelease version : " + prereleaseVersion);
    prereleaseVersion = "-" + prereleaseVersion;

    println("Enter buildMetadata Default is empty");
    String buildMetadata = System.in.newReader().readLine();
    if (buildMetadata != "") {
        buildMetadata = "+" + buildMetadata;
    }
    println("buildMetadata : " + buildMetadata);

    String applicationVersion = major + "." + minor + "." + patch + prereleaseVersion + buildMetadata;
    String buildDirPath = "./dist/${(new Date()).format('yyyy-MM-dd')}" + "/" + applicationVersion + "/";

	version = applicationVersion;

    project.file(buildDirPath).mkdir()

        bootJar {
        destinationDir = new File(buildDirPath)
        archiveBaseName = "my-application"
        manifest {
            attributes(
                    "Application-Version": applicationVersion,
                    "Build-Date": "${(new Date()).format('yyyy-MM-dd HH:mm:ss')}",
            )
        }
    }

    writeVersion(major, minor, patch);

    println("----------------------------------------------------------------------------------------------------")
    println("--------------------------------|                                 |---------------------------------")
    println("--------------------------------|           Build Result          |---------------------------------")
    println("--------------------------------|                                 |---------------------------------")
    println("----------------------------------------------------------------------------------------------------")
    println("Build version : " + applicationVersion);
    println("Build result path : " + buildDirPath);
    println("----------------------------------------------------------------------------------------------------")
    println("----------------------------------------------------------------------------------------------------")
    println("----------------------------------------------------------------------------------------------------")
}
```

</br>

## 정상 동작 확인
정상 동작 확인을 위해서는 gradle build 명령을 사용하여 빌드해야합니다

[##_Image|kage@NdQH1/btrhEElj9jz/BkjUp0yqadcQaUxPIzZxQ1/img.png|alignCenter|width="100%"|_##]



