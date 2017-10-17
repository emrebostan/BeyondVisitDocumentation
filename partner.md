[Tüm Dökümantasyonlar](https://github.com/KozaDigital/BeyondVisitDocumentation/blob/master/README.md)

BeyondVisit Partner entegrasyonu
===================

Entegrasyon aşamaları 
1. [Kullanıcı oluşturma](#user)
2. [Login Tokeni Alma](#anahtar)
3. [Oturum Açma](#oturum)



<a name="user"></a>
Kullanıcı oluşturma 
===================================

<a href="https://app.getpostman.com/run-collection/9642b870d3eebd8f4c17" target="_blank">![Run in Postman](https://run.pstmn.io/button.svg)</a>


**Servis Adresi:** ``http://panel.beyondvisit.com/external/CreateUser``

adresine aşağıdaki parametreleri içeren bir post isteği atmanız gerekiyor.
```json    
    {
        "apikey":"<api_key>",
        "sign":"<MD5(apikey+secretkey)>",
        "user":
        {
            "Name":"John",
            "Surname":"Doe",
            "Email":"johndoe@example.com",
            "Site":"http://www.example.com"
        }
    }
```
## Kullanımı
**apikey**:
*Type: String,*
*MaxLength: 32*

>Mail ile iletilen api anahtarınız


----------


**sign**:
*Type: String,*
*MaxLength: 32*

>Api anahtarınız ile SecretKey'inizin tek bir stringe birleştirilip MD5 ile hashlenmiş halidir

----------


**user**:
*Type: Object,*

----------


**user.Name**:
*Type: String,*
*MaxLength: 50*
>Açılmak istenen kullanıcının ismi

----------


**user.Surname**:
*Type: String,*
*MaxLength: 50*
>Açılmak istenen kullanıcının soyismi

----------


**user.Email**:
*Type: String,*
*MaxLength: 254*
>Açılmak istenen kullanıcının e-posta adresi

----------


**user.Site**:
*Type: String,*
*MaxLength: 2000*
>Açılmak istenen kullanıcının sahip olduğu site anasayfa url si



Sonuç
======
Bu istekten 2 farklı sonuç alabilirsiniz;

## Başarısız
```json
    { 
        "status" : false,
        "code": 1,
        "message" : "The 'user.Name' property should not be empty" 
    }
```
`status` ***true*** olmadığı sürece kullanıcı oluşturma işlemi başarısız sonuçlanmış demektir.Hata mesajı örnekteki gibi belirtilir.

Hata kodları ve açıklamaları
============================
| code | message                                                            |
|------|--------------------------------------------------------------------|
| 1    | The 'user.Site' property should start with 'http://' or 'https://' |
| 2    | The'user.Name' property should not be empty                        |
| 3    | The 'user.Surname' property should not be empty                    |
| 4    | The 'user.Email' property should not be empty                      |
| 5    | Authentication failed                                              |
| 6    | ApiKey not found                                                   |


## Başarılı
```json
    { 
        "status" : true,
        "message" : "Success", 
        "userToken" :  "<user unique token>"
    }
```
Başarılı olması durumunda gelen cevaptaki `userToken` alanını kullanıcı için kaydetmelisiniz.Daha sonra bu token kullanıcı girişi için kullanmanız gereken _parametre_ dir.

<a name="anahtar"></a>
Login Tokeni Alma 
================

<a href="https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/739da96bea5df8c10dc9" target="_blank">![Run in Postman](https://run.pstmn.io/button.svg)</a>

Kullanıcı girişi yapılmak istendiği zaman önce login tokeni üretmelisiniz. 
Login tokenini üretek için ``http://panel.beyondvisit.com/external/LoginToken`` servis adresine aşağıdaki parametreleri içeren bir post isteği atmanız gerekmektedir.
```json
    {
        "apiKey":"<api_key>",
        "sign":"<MD5(apikey+secretkey)>",
        "userToken": "<userToken>"
    }
```
## Kullanımı
**userToken**
*Type: String,*
*MaxLength: 32*
>CreateUser servis metodunun sonucu olarak dönen userToken değeridir.


Sonuç
======
Bu istekten 2 farklı sonuç alabilirsiniz;

## Başarısız
```json
    { 
        "status"  : false,
        "code"    : 1, 
        "message" : "User not found with token: [userToken]" 
    }
```
`status` ***true*** olmadığı sürece token oluşturma işlemi başarısız sonuçlanmış demektir.Hata mesajı örnekteki gibi belirtilir.

Hata kodları ve açıklamaları

| code | message                                 |
|------|-----------------------------------------|
| 1    | User not found with token: [userToken]  |
| 2    | Authentication failed                   |
| 3    | ApiKey not found                        |

## Başarılı
```json
    { 
        "status" : true, 
        "message" : "Success", 
        "loginToken" : "[user_login_token]",
        "userCode" : "[user_code]"
    }
```
Başarılı olması durumunda gelen cevaptaki `loginToken` alanını kullanının login işlemini gerçekleştirmek için kullanılacaktır.

`userCode` alanı scriptlerde kullanılacak olan kullanıcı kodudur.


<a name="oturum"></a>
BeyonVisit üzerinde oturum açma 
=============================

Login tokeni alma işlemi gerçekleştirdikten sonra alınan `loginToken` değeri kullanılarak kullanıcının browserini aşağıdaki url ye yönlendirmelisiniz.

    http://panel.beyondvisit.com/external/Login/{loginToken}

Tüm adımların doğru gerçekleştirilmesi durumunda kullanıcı panele erişebilir olacaktır.

Sisteminiz üzerinden login işleminin daha sonra tekrar gerçekleştirilmek istendiğinde `Login Token Alma` işleminden itibaren bu adımlar tekrarlanmalıdır.
