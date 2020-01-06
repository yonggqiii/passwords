# passwords

> A deterministic password generator

## How do I use this?

All you need to do is to head to the website and enter a simple but thoughtful password in the green text box. Then, click on the "Copy to clipboard" button and paste it into the desired password box.

If you need to view your simple or generated password, click on the corresponding buttons. Be sure that no one's watching!

## How is this implemented?

There's no real science to this. It is simply taking your string of text and hashing it using SHA512. The result is limited to 60 characters which most platforms support.

## Is this secure?

Your passwords aren't stored anywhere. That means, unlike password managers, if you lose your account or mobile phone, you'll still be able to access your passwords--if you remember the simple passwords, that is.

Also, there's no way for attackers to get your information, unless they have your simple password.

However, don't forget that as long as an attacker has a password (simple or otherwise) to your account, they'll still be able to enter it. But that of course is the point of this simple program: to make it as difficult as possible for attackers to guess your password.

## Develop and Build

``` bash
# install dependencies
$ npm run install

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm run start

# generate static project
$ npm run generate
```

For detailed explanation on how things work, check out [Nuxt.js docs](https://nuxtjs.org).
