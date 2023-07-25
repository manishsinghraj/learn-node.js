# What is Node.js & Why use it?

> **Node.js** is JS runtime environment Built on google's open source *V8* JS engine.


> **Node.js** is an open-source and cross-platform JavaScript runtime environment. It is a popular tool for almost any kind of project!

> **Node.js** runs the V8 JavaScript engine, the core of Google Chrome, outside of the browser.

>  A **Node.js** app runs in a single process, without creating a new thread for every request.


> When **Node.js** performs an I/O operation, like reading from the network, accessing a database or the filesystem, instead of blocking the thread and wasting CPU cycles waiting, Node.js will resume the operations when the response comes back.


--------------------------------------------

# Read File sync

```js
//Blocking, synchronous way
const fs = require('fs');
const textIn = fs.readFileSync('./txt/input.txt','utf-8');
console.log(textIn)
```
>**UTF-8** is one of the Unicode Transformation Formats which convert a Unicode “codepoint” or hexadecimal integer into a particular sequence of bytes.
UTF-8 encodes Unicode characters into a sequence of 8-bit bytes. The standard has a capacity for over a million distinct codepoints and is a superset of all characters in widespread use today. By comparison, ASCII (American Standard Code for Information Interchange) includes 128 character codes.
--------------------------------------------

# Write File sync

```js
//Blocking, synchronous way
const fs = require('fs');
const textOut = `This is what we know about avocado: ${textIn}.\nCreated on ${Date.now()}`;
fs.writeFileSync('./txt/output.txt', textOut);
console.log("File is written");
```

------------------------------------------

# Blocking and Non-Blocking: Asynchronous Nature of Node.js

------------------------------------------

# Reading and Writing Files Asynchronously

```js
//Non-Blocking, Asynchronous way
//Beginer way,but this is callback hell, There is a beter way. Like Promises, Async/Await
const fs = require('fs');
fs.readFile('./txt/start.txt','utf-8', (err, data1) =>{         //ES6 Function
    fs.readFile(`./txt/${data1}.txt`,'utf-8', (err, data2) =>{
        console.log(data2);
        fs.readFile(`./txt/append.txt`,'utf-8', (err, data3) =>{
            console.log(data3);

            fs.writeFile('./txt/final.txt', `${data2}\n${data3}`, 'utf-8', (err)=>{
                console.log('your file has been written!');
            });
        });
    });
});
console.log("will read file");
```
> Learn about ES6 
------------------------------------------

# Creating simple Web Server
```js
//SERVER
const http = require('http');
const server = http.createServer((req, res) =>{
    res.end("Hello from the server!")
});

server.listen(8000, '127.0.0.1', () =>{
    console.log("Listening to request on port 8000");
});
```
>127.0.0.1:8000
------------------------------------------
# Routing
```js
//Routing
const http = require('http');
const url = require('url');
const server = http.createServer((req, res) =>{
    const pathName = req.url;
    if(pathName === '/overview'){
        res.end("Hello from the OVERVIEW!");
    }else if(pathName === '/product'){
        res.end("Hello from the PRODUCT!");
    }else{
        res.writeHead(404,{
            'Content-type':'txt/html',
            'my-own-header':'Hello'
        });
        res.end("<h1>Page not found!</h1>");
    }
});

server.listen(8000, '127.0.0.1', () =>{
    console.log("Listening to request on port 8000");
});
```
> console.log(req.url); When you refresh browser with address - http://127.0.0.1:8000/overview
logs Below:
/overview
/favicon.ico

------------------------------------------

# Building a (Very) Simple API

> What is API?

```js
//Building a simple API
//efficient way is below code after this
const http = require('http');
const fs = require('fs');

const server = http.createServer((req, res) =>{
    const pathName = req.url;
    if(pathName === '/overview'){
        res.end("Hello from the OVERVIEW!");
    }else if(pathName === '/product'){
        res.end("Hello from the PRODUCT!");
    }else if(pathName === '/api'){
        fs.readFile(`${__dirname}/dev-data/data.json`,'utf-8',(err,data) => {
            res.writeHead(200,{
                'Content-type':'application/json'
            });
            res.end(data); //Always accepts String, so No JSON.Parse as below

            // const productData = JSON.parse(data);
            // console.log(productData);
        });
    } else{
        res.writeHead(404,{
            'Content-type':'txt/html',
            'my-own-header':'Hello'
        });
        res.end("<h1>Page not found!</h1>");
    }
});

server.listen(8000, '127.0.0.1', () =>{
    console.log("Listening to request on port 8000");
});
```
>if we send it as json object we get below error in terminal [ERR_INVALID_ARG_TYPE]: The "chunk" argument must be of type string or an instance of Buffer or Uint8Array. Received an instance of Array

```js
//efficient way : reading file will always execute when client always hits the api again and again, not good thing hence we will move readfile on top, and it will execute once when code starts, also using sync it wont create problem here, 
const http = require('http');
const fs = require('fs');

const data = fs.readFileSync(`${__dirname}/dev-data/data.json`,'utf-8');
const dataObj = JSON.parse(data);


const server = http.createServer((req, res) =>{
    const pathName = req.url;
    if(pathName === '/overview'){
        res.end("Hello from the OVERVIEW!");
    }else if(pathName === '/product'){
        res.end("Hello from the PRODUCT!");
    }else if(pathName === '/api'){
        res.writeHead(200,{'Content-type':'application/json'});
        res.end(data); //Always accepts String, so No JSON.Parse 
    } else{
        res.writeHead(404,{
            'Content-type':'txt/html',
            'my-own-header':'Hello'
        });
        res.end("<h1>Page not found!</h1>");
    }
});

server.listen(8000, '127.0.0.1', () =>{
    console.log("Listening to request on port 8000");
});
```

#Our Own Module
```js
// const http = require('http');
// const fs = require('fs');


//We create a modulefolder and move function that is declared in indec.js to respective function folder
const replaceTemplate = require('./module/replaceTemplate');

//under module foler created replaceTemplate.js 
module.exports = (temp, product) => {
  let output = temp.replace(/{%PRODUCTNAME%}/g, product.productName);
  output = output.replace(/{%IMAGE%}/g, product.image);
  output = output.replace(/{%PRICE%}/g, product.price);
  output = output.replace(/{%FROM%}/g, product.from);
  output = output.replace(/{%NUTRIENTS%}/g, product.nutrients);
  output = output.replace(/{%QUANTITY%}/g, product.quantity);
  output = output.replace(/{%DESCRIPTION%}/g, product.description);
  output = output.replace(/{%ID%}/g, product.id);
  
  if(!product.organic) output = output.replace(/{%NOT_ORGANIC%}/g, 'not-organic');
  return output;
}
```

#Introduction to NPM and the package.json File
>what is npm?


#Theory
#Introducion to backend web-development

![Alt text](image.png)

![Alt text](image-1.png)

###DNS in nutshell
>DNS, or the Domain Name System, translates human readable domain names (for example, www.amazon.com) to machine readable IP addresses (for example, 192.0.2.44).

>A user opens a web browser, enters www.example.com in the address bar, and presses Enter.
The request for www.example.com is routed to a DNS resolver, which is typically managed by the user's Internet service provider (ISP), such as a cable Internet provider, a DSL broadband provider, or a corporate network.
The DNS resolver for the ISP forwards the request for www.example.com to a DNS root name server.
The DNS resolver for the ISP forwards the request for www.example.com again, this time to one of the TLD name servers for .com domains. The name server for .com domains responds to the request with the names of the four Amazon Route 53 name servers that are associated with the example.com domain.
The DNS resolver for the ISP chooses an Amazon Route 53 name server and forwards the request for www.example.com to that name server.
The Amazon Route 53 name server looks in the example.com hosted zone for the www.example.com record, gets the associated value, such as the IP address for a web server, 192.0.2.44, and returns the IP address to the DNS resolver.
The DNS resolver for the ISP finally has the IP address that the user needs. The resolver returns that value to the web browser. The DNS resolver also caches (stores) the IP address for example.com for an amount of time that you specify so that it can respond more quickly the next time someone browses to example.com. For more information, see time to live (TTL).
The web browser sends a request for www.example.com to the IP address that it got from the DNS resolver. This is where your content is, for example, a web server running on an Amazon EC2 instance or an Amazon S3 bucket that's configured as a website endpoint.
The web server or other resource at 192.0.2.44 returns the web page for www.example.com to the web browser, and the web browser displays the page.

https://aws.amazon.com/route53/what-is-dns/#:~:text=The%20Internet's%20DNS%20system%20works,These%20requests%20are%20called%20queries

https://www.cloudflare.com/learning/dns/what-is-dns/

https://bunny.net/academy/dns/what-is-dns-domain-name-system/


>what is tcp/ip?