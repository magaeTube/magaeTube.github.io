---
title:  "[Java] Lombok"
date:   2024-03-08 00:00:00 +0900
categories:
  - Java
tags:
  - Java
  - Lombok
toc: true
toc_sticky: true
toc_label: "Lombok"
header-mask: true
---

&nbsp;안녕하세요 마개입니다.  
Java를 이용하여 모델을 개발하는데 사용되는 Lombok에 대해 알아봅니다.
{: .notice--info}

<br><br>

# Lombok

&nbsp;Lombok이란 Java 라이브러리 중 하나로 Model 클래스 등에서 많이 사용하는 `getter`, `setter`, `toString` 등의 메서드 코드를 줄여주는 라이브러리입니다. 순수하게 Java로 코딩을 한다고 하면 여러 개의 getter, setter, toString 등의 메서드를 반복하게 되는데 이는 번거로울 수 있습니다. Lombok을 사용하면 annotation으로 가볍게 정의하고 getter, setter 등의 메서드는 컴파일된 결과물인 .class에 생성되게 됩니다.  
&nbsp;기존에 Java에서 Model을 구성할 때 어떻게 했는지 봅시다.

```java
// UserModel.java
public class UserModel {
    private String userId;
    private String userName;
    private String gender;
    private Integer age;
    private String emailAddress;

    public UserModel() {}

    public UserModel(String userId, String userName, String gender, Integer age, String emailAddress) {
        this.userId = userId;
        this.userName = userName;
        this.gender = gender;
        this.age = age;
        this.emailAddress = emailAddress;
    }

    public String getUserId() {
        return this.userId;
    }

    public void setUserId(String userId) {
        this.userId = userId;
    }

    public String getUserName() {
        return this.userName;
    }

    public void setUserName(String userName) {
        this.userName  = userName;
    }

    public String getGender() {
        return this.gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public Integer getAge() {
        return this.age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getEmailAddress() {
        return this.emailAddress;
    }

    public void setEmailAddress(String emailAddress) {
        this.emailAddress = emailAddress;
    }
}

// Main.java
public class Main {
    public static void main(String[] args) {
        UserModel user = new UserModel();
        user.setUserId("def456");
        user.setUserName("David");
        user.setGender("M");
        user.setAge(20);
        user.setEmailAddress("def456@gmail.com");

        String userID = user.getUserId();
        String userName = user.getUserName();
        String userGender = user.getGender();
        Integer userAge = user.getAge();
        String userEmailAddress = user.getEmailAddress();
        System.out.println("User ID: " + userID);
        System.out.println("User Name: " + userName);
        System.out.println("Gender: " + userGender);
        System.out.println("Age: " + userAge.toString());
        System.out.println("Email Address: " + userEmailAddress);
    }
}
```

위와 같이 Getter와 Setter를 만들어서 메인 클래스에서 이용할 수 있습니다. 이러면 변수의 개수만큼 늘어나기 때문에 Lombok을 이용하면 간단하게 만들 수 있습니다.

<br><br>

# 설치 및 적용

&nbsp;IntelliJ에 Lombok을 적용해봅니다.  

![Lombok](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/30422b66-db1a-4029-8b01-0433cfb23146)

&nbsp;IntelliJ의 설정 - 플러그인에서 `lombok`을 검색해서 설치합니다. 위 이미지에서는 이미 설치했기 때문에 Installed에서 보이게 됩니다.  
&nbsp;IntelliJ에서 설치한 후에는 build.gradle에 추가해야 합니다. 저는 Gradle을 이용했기 때문에 이 파일에 추가합니다.  

```
dependencies {

    implementation('org.projectlombok:lombok')
    annotationProcessor('org.projectlombok:lombok')

    testImplementation('org.projectlombok:lombok')
    testAnnotationProcessor('org.projectlombok:lombok')
}
```

&nbsp;위와 같이 설정하고 IntelliJ에서는 어노테이션 프로세싱 활성화를 해줘야 lombok을 사용할 수 있습니다. IntelliJ의 설정 -  Build, Execution, Deployment - Compiler - Annotation Processors 메뉴로 들어갑니다.

![Compiler](https://github.com/magaeTube/magaeTube.github.io/assets/78892113/89b306ea-1a21-48ad-adc5-fd698fcbc79a)

&nbsp;해당 메뉴에서 "Enable annotation processing"을 선택하고 적용합니다. 적용한 후에 Gradle를 다시 빌드하면 적용됩니다. 적용이 완료되었으니 Lombok에 유용한 어노테이션들을 확인해봅니다.

<br><br>

# 유용한 어노테이션

&nbsp;Lombok에는 여러 어노테이션을 제공하여 도움을 주고 있습니다. 여기서 많이 사용하는 어노테이션에 대해 알아봅니다.

<br>

## @Getter / @Setter

&nbsp;Lombok에서 가장 많이 활용되는 어노테이션으로 클래스명 위에 적용시키면 모든 변수들에 적용이 가능하고 변수 이름 위에 적용하면 해당 변수들만 적용 가능합니다. 위에서 만들었던 UserModel에 Lombok을 적용해봅니다.

```java
import lombok.Setter;
import lombok.Getter;

@Setter
@Getter
public class UserLombokModel {
    private String userId;
    private String userName;
    private String gender;
    private Integer age;
    private String emailAddress;
}
```

&nbsp;이렇게 간단하게 코드를 작성하는 것만으로도 Setter와 Getter를 이용할 수 있습니다.

<br>

## @AllArgsConstructor

&nbsp;AllArgsConstructor는 모든 변수를 사용하는 생성자를 자동 완성시켜주는 어노테이션입니다.

```java
import lombok.Getter;
import lombok.AllArgsConstructor;

@Getter
@AllArgsConstructor
public class UserLombokModel {
    private String userId;
    private String userName;
    private String gender;
    private Integer age;
    private String emailAddress;

    /*
    // 다음과 같이 모든 변수를 사용하는 생성자를 완성시켜줍니다.
    public UserLombokModel(String userId, String userName, String gender, Integer age, String emailAddress) {
        this.userId = userId;
        this.userName = userName;
        this.gender = gender;
        this.age = age;
        this.emailAddress = emailAddress;
    }
    */
}
```

&nbsp;위와 같이 클래스에 `@AllArgsConstructor`를 지정해주면 됩니다.

<br>

## @NoArgsConstructor

&nbsp;NoArgsConstructor는 기본 생성자를 자동 완성시켜주는 어노테이션입니다. 

```java
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class UserLombokModel {
    private String userId;
    private String userName;
    private String gender;
    private Integer age;
    private String emailAddress;

    /*
    // 다음과 같이 기본 생성자를 완성시켜줍니다.
    public UserLombokModel() {
        
    }
    */
}
```

&nbsp;위와 같이 클래스 위에 `@NoArgsConstructor`를 지정해주면 됩니다.

<br>

## @RequiredArgsConstructor

&nbsp;특정 변수만을 활용하는 생성자를 자동 완성시켜주는 어노테이션입니다. 추가할 변수에 `@NonNull` 어노테이션을 붙이거나 final로 선언하면 의존성을 주입할 수 있습니다.

```java
import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public class UserLombokModel {

    @NonNull
    private String userId;
    private final String userName;
    private String gender;
    private Integer age;
    private String emailAddress;

    /*
    // 다음과 같이 지정한 변수를 이용한 생성자를 완성시켜줍니다.
    public UserLombokModel(String userId, String userName) {
        this.userId = userId;
        this.userName = userName;
    }
    */
}
```

&nbsp;위와 같이 @NonNull 또는 final을 이용하여 생성자를 만들 수 있습니다.

<br>

## @ToString

&nbsp;`@ToString` 어노테이션을 활용하면 클래스 변수들에 대한 ToString 메소드를 자동으로 완성시켜줍니다. 여기서 제외할 변수에 대해서는 @ToString.Exclude 어노테이션을 붙여주면 됩니다.

```java
import lombok.ToString;
import lombok.AllArgsConstructor;

@ToString
@AllArgsConstructor
public class UserLombokModel {

    @ToString.Exclude
    private String userId;
    private final String userName;
    private String gender;
    private Integer age;
    private String emailAddress;

}
```

<br>

## @Data

&nbsp;`@Data` 어노테이션을 사용하면 @Getter, @Setter, @RequiredArgsConstructor, @ToString, @EqualsAndHashCode를 자동 완성시켜줍니다. 편하긴 하지만 너무 무겁기 때문에 실무에서는 지양합니다.