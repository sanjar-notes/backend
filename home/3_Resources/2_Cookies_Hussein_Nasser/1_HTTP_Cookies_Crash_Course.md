# 1. HTTP Cookies crash course
Created Tuesday 27 June 2023 at 02:41 am

Video link: https://youtu.be/sovAIX4doOE

## What I understood
Cookies are used to for:
1. Authentication state on clients
2. For saving meta preferences (like language, theme)
3. For tracking users

## Section 1: Creating Cookies
There are two ways to set cookies:
1. Using client side JS, using `document.cookie`
    ```js
    document.cookie = 'sanjarIsLoggedIn=12;'
    document.cookie = 'darkMode=true;'

	// catch, only one cookie can be set at a time
	// so a loop may be needed
    ```
2. Using the server - using the `set-cookie` header. This can be done in any kind of response - GET, POST etc.
    ```js
    const app = require('express')();
    app.get('/', (req, res) => {
    
      res.setHeader(
          'set-cookie', 
	      ['sanjarIsLoggedIn=true', 'darkMode=true']
	  );

      res.send("Cookies set");
    })

    app.listen(3000, () => console.log('App running on port 3000'));
	```

Note: not worrying about deleting cookies, as well as parser tools.


## Section 2: Cookie Properties
- Cookies are sent with every request - GET, POST every request. \*(there are exceptions, ignoring them).
- Cookie scopes - cookies are bound to "scopes", and not accessible by other "scopes". "Scopes" are determined by
	- `domain` - by default, different domain don't share cookies. Example:`www.example.com`, `example.com`, `about.example.com` don't share cookies. <details><summary>Can be configured to be lenient</summary>This can be configured though to allow for cookies to be shared across these sites
	  ```js
	  res.setHeader( 'set-cookie', ['sanjarIsLoggedIn=true', 'darkMode=true; domain=.example.com'] );
	  // darkMode will be sent for all subdomains of 'example.com'
	    ````
	 </details>
	- `path` - by default, all cookies are sent for all paths (i.e. request paths). <details><summary>Can be configured to be restrictive</summary>This is optional, and can be configured when the cookie is set. This way, one can add exceptions to when the cookie is sent for a domain, but different paths on it. Example:
	     ```js
		res.setHeader(
	        'set-cookie', 
		    ['sanjarIsLoggedIn=true', 'darkMode=true; path=/path2']
		);
		// darkMode will be sent only if request is sent from '/path2'
	    ```
	    Can be used to optimize request size and organize cookies if the app uses a lot of them.
     </details>
- `expires`, `max-age` - if these are unspecified, the cookie will be deleted when the browser is closed (such cookies are called 'session' cookies).
- `samesite` - a useful restriction. If set to `strict`, cookies will be sent only for requests (including link clicks) that were initiated from the same site (think address bar) that set the cookie. *Better understood through example* - suppose the link `bankofamerica.com/transfer?toAccount=12345&amount=4000` is there on a shady site. The shady site will not know bankofamerica cookies, but since it made us click the link, the transfer will happen (**since cookies are sent by default even if current site is different**). `samesite=strict` prevents this. <details><summary>More</summary>
	- This works even if `domain` is very lenient, or even if a resource to original site (say image) is embedded on this other site (cookies won't be sent).
	- Of course, loading a fresh page (i.e. fresh address + Enter) will work - cookies will be sent.
	  </details>
- `Httponly` - boolean property. discussed ahead.
- `Secure` - boolean property. discussed ahead.


## Section 3: Cookie Types
Note: These are just terms used in web development, they are not defined in a standard.

- Session cookie - A cookie that gets deleted when the browser is closed. Example - a cookie that doesn't have a `expires` and `max-age` property.
- Permanent cookie - a cookie that won't get deleted when the browser is closed. Example - a cookies that has a `expires` or `max-age` property. It's the opposite of a "session cookie".
- `Httponly` cookies - these are cookies that are not accessible via browser JS (`document.cookie` doesn't show a cookie set like this). The server can set cookies, and receives them with requests, as usual. To set them: just use the `Httponly` cookie property, at the end (value not needed) `darkMode=true; Httponly`
- Secure cookie - cookies that have the `Secure` property will be set only if the site has HTTPS.
- Third party cookie - a cookie that deliberately has no `samesite=strict` and a very lenient `domain` property. The intent by the setter site here is to transmit the cookie to as many sites as possible. This way, a site receives cookies set by other sites, i.e. it tracks what sites the user visits. Of course, the sites involved need to agree upon name and values of these "tracking" cookies. Example: `youtube.com` sets a very lenient cookie, and when I click a Flipkart product link on youtube, Flipkart gets this lenient cookie. Now, flipkart knows that the link was from youtube.com and that also that I visited youtube.
- Zombie cookie - a nagging way to track users. browsers have a storage area for 'E tags', which cannot be cleared easily (this was true in the past, atleast). This area is meant for caching stuff when a resource is requested. If a request is made to the same resource, the 'E tag' is sent automatically, the server compares the received 'E tag' to the resource's hash, and if it matches, sends 304 (Not modified). **In other words**, if the server generated the first E  tag using the IP address + resource content, and you clear cookies and make the request again, it will know that it's you (since it will generate the hash from IP address + content and know that such a hash was generated already). **Fun fact: cookies are in no way involved in this, but due to 'tracking' nature, it's termed like a zombie cookie**.


## Section 4: Cookie Security
1. Steal cookies using `document.cookie`
	- How - find a way to run client side code on the victim site you wish to attack, by adding an add banner to the victim site or something.
	- Fix - use `Httponly` property on sensitive cookies, so they can't be accessed by client side code.
1. CSRF (Cross Site Request Forgery) - a valid "action" link on a shady site (bankofamerica example). 
	- How - add a valid "action" link and make the user click it (trick them into thinking it's a harmless link)
	- Fix - use the `samesite=strict` property, so cookies are not sent if you're not on the original site.