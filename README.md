# Documentation Bank API

Welcome to the official API of project 3/4. Well, it is not actually official, but we, the Soviet Union, will use it. If you don't, we will call the Gulag. We are very glad that you are just as excited as we are to use this API.

# Table of contents
1. [How to use the API](https://github.com/projectbank/Documentatie#how-to-use-the-api)
    1. [Create a new client](https://github.com/projectbank/Documentatie#create-a-new-client)
        1. [Request](https://github.com/projectbank/Documentatie#request)
        1. [Response](https://github.com/projectbank/Documentatie#response)
            1. [Success](https://github.com/projectbank/Documentatie#success)
            1. [Error](https://github.com/projectbank/Documentatie#error)
    1. [Check credentials](https://github.com/projectbank/Documentatie#check-credentials)
        1. [Request](https://github.com/projectbank/Documentatie#request-1)
        1. [Response](https://github.com/projectbank/Documentatie#response-1)
            1. [Success](https://github.com/projectbank/Documentatie#success-1)
            1. [Error](https://github.com/projectbank/Documentatie#error-1)
    1. [Check saldo](https://github.com/projectbank/Documentatie#check-saldo)
        1. [Request](https://github.com/projectbank/Documentatie#request-2)
        1. [Response](https://github.com/projectbank/Documentatie#response-2)
            1. [Success](https://github.com/projectbank/Documentatie#success-2)
            1. [Error](https://github.com/projectbank/Documentatie#error-2)              
    1. [Withdraw money](https://github.com/projectbank/Documentatie#withdraw-money)
    1. [Transfer money](https://github.com/projectbank/Documentatie#transfer-money)
        1. [Request](https://github.com/projectbank/Documentatie#request-3)
        1. [Response](https://github.com/projectbank/Documentatie#response-3) 
            1. [Success](https://github.com/projectbank/Documentatie#success-3)
            1. [Error](https://github.com/projectbank/Documentatie#error-3)        

## How to use the API

The API is very easy to use. All of the requests must be made as POST request. Because we are communists, we will always give you something back to thank you for your work. This will be done in the form of JSON responses.

All of the calls should be made to https://leutertrekkers.nl/api. The example URLs are GET requests, so you can test easier, but the official API requires you to use POST requests.

### Create a new client

To create a new, you have to make a call to https://bowero.nl/api/clients/add.php. This requires three POST parameters.

1. __The NUID of the client.__ This should be 8 characters long.
2. __The PIN of the client.__ Of course, you may pick your own PIN. This should be 4 numbers long. The database will refuse everything that is not an integer.
3. __The name of the client.__ We want to know the name of your client. Please try to avoid customers with names that are longer than 40 characters. We find this unreasonably difficult.

#### Request

As you might see, this is not a very difficult request. As you may also note, no one mentions the IBAN. That is being generated server side. It is also your response, unless something went wrong.

You can test your requests online on websites like these: https://www.codepunker.com/tools/http-requests
Make sure to add the right fields to the request.

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

An example request will look like this: http://bowero.nl/api/clients/saldo.php?nuid=D0CC76AD&pin=0000.

It has 2 return possibilites: a `saldo` or an `error`.

#### Response

##### Success

When your call is succesful, which it should be, you can expect a response like this:

```json
{
saldo: 300
}
```

##### Error

However, when your credentials are wrong, you will be responded this:

```json
{
error: "Could not log in."
}
```

This will count forward to your log in attempts. Please check before sending a request to check the balance.

### Withdraw money

To withdraw money from your account, you just have to transfer money without a recipient. The default settings will ensure that your money gets perfectly lost. Follow the instructions at [Transfer money](https://github.com/projectbank/Documentatie#transfer-money), but do not send a `recipient`.

### Transfer money

Transferring is probably the most difficult API call. That explains just how easy the API is. It allows 4 parameters, although you only need 3 to withdraw, if that is what you like.

1. __The NUID of the client__ This is the client that wants to transfer money
2. __The PIN of the client__ To allow the client to transfer money, we need his PIN.
3. __The amount of money to transfer__ This will be withdrawn from the client's account and transfered to the recipient.
4. __The IBAN of the recipient _(optional)___ The recipient only needs an IBAN. We will handle the rest. If you don't send a recipient, we will just withdraw the money and you can give it in cash to the client.

#### Request

An example request would look like this: http://bowero.nl/api/clients/transfer.php?nuid=D0CC76A5&pin=0901&amount=300&recipient=SU65USSR182763. 

The result is the balance after the transfer. Just like with the balance requests, this can return `0`. If this happens, your transfer was succesful.

#### Response

##### Success

The response will contain a `saldo` if your transfer succeeded. This will be the balance after the transfer.

```json
{
saldo: 300
}
```

##### Error

Sometimes something goes wrong. In that case, you will receive a useful error message. For example, this is a return value you could get.

```json
{
error: "The recipient does not exist."
}
```
