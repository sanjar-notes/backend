# 2. SameSite cookie attribute
Created Tuesday 27 June 2023 at 02:41 am

Video link - [  
SameSite Cookie Attribute Explained by Example (Strict, Lax, None & No SameSite)](https://youtu.be/aUF2QCEudPo)


## All variations of `samesite`
1. `strict`
	- embed requests from shady site - cookies won't be sent
	- link click on shady site (even if a new page is loaded) - cookies won't be sent
2. `lax`
	- embed requests from shady site - cookies won't be sent
	- link click on shady site - cookies *will* be sent
3. `none` - cookies *will* be sent in both cases - embeds and link clicks
4. Absent - if `samesite` is absent, it's treated as `samesite=lax`. 
	- Was first introduced in Chrome 80