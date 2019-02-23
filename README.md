# Documentation Bank API

Welcome to the official API of project 3/4. Well, it is not actually official, but we, the Soviet Union, will use it. If you don't, we will call the Gulag. We are very glad that you are just as excited as we are to use this API.

# Table of contents
1. [How to use the API](https://github.com/projectbank/Documentatie/master#how-to-use-the-api)
    1. [Create a new client](https://github.com/projectbank/Documentatie/master#create-a-new-client)
        1. [Request](https://github.com/projectbank/Documentatie/master#request)
        1. [Response](https://github.com/projectbank/Documentatie/master#response)
            1. [Success](https://github.com/projectbank/Documentatie/master#success)
            1. [Error](https://github.com/projectbank/Documentatie/master#error)
    1. [Check credentials](https://github.com/projectbank/Documentatie/master#check-credentials)
        1. [Request](https://github.com/projectbank/Documentatie/master#request-1)
        1. [Response](https://github.com/projectbank/Documentatie/master#response-1)
            1. [Success](https://github.com/projectbank/Documentatie/master#success-1)
            1. [Error](https://github.com/projectbank/Documentatie/master#error-1)
    1. [Check saldo](https://github.com/projectbank/Documentatie/master#check-saldo)
        1. [Request](https://github.com/projectbank/Documentatie/master#request-2)
        1. [Response](https://github.com/projectbank/Documentatie/master#response-2)     

## How to use the API

The API is very easy to use. All of the requests must be made as GET request. Because we are communists, we will always give you something back to thank you for your work. This will be done in the form of JSON responses.

All of the calls should be made to https://bowero.nl/api/.

### Create a new client

To create a new, you have to make a call to https://bowero.nl/api/clients/add.php. This requires three GET parameters.

1. __The NUID of the client.__ This should be 8 characters long.
2. __The PIN of the client.__ Of course, you may pick your own PIN. This should be 4 numbers long. The database will refuse everything that is not an integer.
3. __The name of the client.__ We want to know the name of your client. Please try to avoid customers with names that are longer than 40 characters. We find this unreasonably difficult.

#### Request

As you might see, this is not a very difficult request. As you may also note, no one mentions the IBAN. That is being generated server side. It is also your response, unless something went wrong.

Example request: http://bowero.nl/api/clients/add.php?nuid=D0CC76AD&name=Example%20Surname&pin=0000

#### Response

##### Success

When your request is succesful, you will get the IBAN as response. For example: 

```json
{
iban: "SU87USSR372196"
}
```

##### Error

An error will always have a key `error`. For example:

```json
{
error: "NUID is not 8 charachters."
}
```

### Check credentials

When a client logs in at your bank, you can check his credentials via our API. To do this, you only have to generate a simple API request. This requires only 2 parameters.

1. __The NUID of the client__ You should always get this from the card to avoid fraud.
2. __The PIN of the client__ This is what you want to check.

The PINs are stored hashed in our database, but you don't have to send the hashed PIN to us. We will handle the checking.

#### Request

An example request will look like this: http://bowero.nl/api/clients/credentials.php?nuid=D0CC76AD&pin=0000

It has 3 return possibilities. 0, 1 and 2.

#### Response

##### Success

When your login attempt is succesful, you will get the following response:

```json
{
status: 0
}
```

This will mean that your credentials were correct. You can now use these credentials to use any of the other API calls. If you don't check this, but just instantly use the other API calls, you will receive errors and the client might be blocked from our system. This is no threat, but a serious security measure.

##### Error

When your credentials are wrong, there are 2 possibilities. When you get this response, your credentials are wrong:

```json
{
status: 1
}
```

However, when you get this response, your card is blocked and you can't use it any longer:

```json
{
status: 2
}
```

This happens when you enter the PIN 3 times wrong. When you enter a correct PIN on a card that is not blocked, we will reset the amount of attempts.

### Check saldo

When a client wants to know his saldo, you can send an easy request to the API. We assume that you have already checked the credentials to avoid problems. This requires only 2 parameters.

1. __The NUID of the client__ You should always get this from the card to avoid fraud.
2. __The PIN of the client__ This should be only the 4 numbers.

#### Request

An example request will look like this: http://bowero.nl/api/clients/saldo.php?nuid=D0CC76AD&pin=0000

It has only 1 return possibility: the saldo. If you enter the wrong credentials, you will be returned `0`. It is your problem to handle that, since you already had the opportunity to check the credentials before calling this action. The wrong PIN will be registered however.

#### Response

When your call is succesful, which it should be, you can expect a response like this:

```json
{
saldo: 300
}
```

However, when your credentials are wrong, you will be responded this:

```json
{
saldo: 0
}
```

As you see, this could also be a valid response. To avoid problems with this, it is strongly encouraged to check the credentials with the method above __before__ checking the balance.
