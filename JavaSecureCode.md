# Java Secure Code

### Input validation:
* black lists - limited
* white lists - much better, regualar expressions 

### SQL injection
* **Danger:**
  * unvalidated dynamic SQL queries in java code 
  * stored procedures that construct dynamic SQL queries using unvalidated user input
  * SQL injection allows the attacker to execute anuthorozed queries, commands with full privileges, 
e.g. steal DB, modify DB, bypass authentication/authorization
* **Remedy:**
  * validate all input in dynamic SQL
  * use parameterezied API for all DB access - PreparedStatement class, 
  * deploy Java application with least priviliges + defence in depth

### Integer Overflow
* **Danger** when performing critical integral calculations, such as finantial applications, and in unmanaged applications that allocate static buffers.
* **Impact:** DoS (application crash), data corruption, remote code execution (integer overflow can result in incorrect indexing into memory regions)
* **Remedy:** precondition testing, upcasting (convert int into long, upcasting can't be used for long), BigInteger object
* **Pitfalls:** integer overflows happen to longs, signed integral types.

### Canonization Issues
* **Danger:** Applications that handle URLs, hostnames, filenames are suseptible to attacks.
* **Impact:** A canonization vulnerability explot can result in: unathorized access, information disclosure, elevation of privilige.
* **Remedy:** statndart representation for input, standard libraries for canonization, validate all inputs using a whitelist approach (check expected type, range, format, and length).
  * Files: java.io.File.getCanonicalPath, java.io.File.getCanonicalFile
  * URLs: encode.canonicalize method
* **Pitfalls:** create your own canonicalization methods, canonicalization after validating input, using relatinve paths.

### Inforamtion Disclosure:
* **Danger:** Occur when an applicationreveals sensitive information to an attacker due to:
improper handling of exceptions, embedding sensitive information in error messages, source code, or comments, inconsistent error messages.
* **Remedy:** 
  * Avoid hard-coding secrets, use generic error messages, never return error messages/stack traces from backend, e.g. DB, encypt sensitive data. It's easy to decompile classes and even obfuscated classes.
 * Handle all exceptions, don't expose  stack trace information, return generic error messages, use the same error message to report the same condition/redirecto to the same page in web app regardless of the cause.

### Race Conditions: 
* ** Danger:** Time of Check Time of Use (TOCTOU) - between the check time and usage time there was a change in a resource condition.    
* **Impact:** DoS, escalation of privilage.
* **Remedy:** 
  * Use a random name when creating a resource
  * Apply access control list (ACL) to enforce privileges, enforce exclusive rights on resources.
  * User the resource immediatly after checking it.
  * Check the origin of a resource.

### Direct Object Reference
* **Danger:** Application exposes a direct reference to a resource object, e.g. file or directory
* **Remedy:** validate access to resources, restrict the application to needed resources only, use lookup table (IntegerAccessReferenceMap, RandomAccessReferenceMap) to reference the resources. 

### Cross-Site Scripting (XSS)
* ** Danger:** 
  * Persistent XSS - malisious code is stored in a data store in the vulnerable web app or resource,
  * Non-persisitent XSS - malisious code uses a refelective aspect of the app, and the user is coerced into performing an action (e.g., click a link) to initiate the attack.
* **Impact:** subversion of application logic in a variety of ways:
compromise of accounts, disclosure of information, execution of malicious code on client system, 
elevation of priviliges.
* **Remedy:** whitelist validation of all untrusted input, use context-sensitive encoding for untrusted output in web responces, use sanitization libraries to remove malicious code from untrusted data.

### Cross-Site Request Forgery (CSRF)/Session riding/Zero click attacks
* ** Danger:** Attacker tricks a user into loading a page with malicious request. In many apps each request automatically sends victim's credentials (e.g. session cookie or authentication credentials). Therefore, the false request cannot be distingueshed from a legitimate request. The request is sent in the context of the victim, and can change the state of the web app.
* ** Impact:** elevation of privilages, i.e., attacker can execute in the context of the victim
* ** Remedy:**
  * Sycnc. Token Approaches: unique per session URL tokens, double submit cookies, encypted tokens
  * Non-Sync. Tocken Approaches: validate the referrer header, validate the origin header, challenge-response
* **Pitfalls:** client-side validation of tokens, assume that CSRF vulnerabilityaffect only GET requests,
assume that multistep transactions are immune from CSRF attack.

### URL Redirection 
* ** Danger:** Redirect logged in users to another page and trick them to enter sensitive information on a malisious web site.
* **Remedy:** avoid using URL redirection (or alert user and give him a choice), avoid redirecting to URLs from an untrusted source, validate all redirection URLs, use mapping values for redirects and forwards, preventing attackers from forcing redirections to malicious websites.

### XPath Injection
* ** Danger:** Application uses unvalidated input to construct an XPath statement to access XML tables, enabling attackers to run arbitrary commands in XML-based databases.
* **Impact:** confidentiality, authentication, authorization, integrity - as sensitive  information can be stored in an XML file.
* **Remedy:** whitelist input validation, use parameterezied XPath and XQuery interfaces, use escaping or encoding routines, do not echo XQuery errors.

### Protecting Data in Java Applications Through Encription
* **Danger:** Java crypto services: JCA and JCE API, Java a/symmetric enciption algorithms: javax.crypto.Cipher.getInstance()
* **Impact:** Create crypto data hashes with salts: java.security.MessageDigest.getInsyance()[set data to hash appended or prepended with a salt value].digest()
* Pitfalls:
  * not using salt (is a random number that is added to the encryption key or to a password to protect them from disclosure) with hashing
  * using weak transformation modules
  * using noncomplaint encyption algorithms
  * creating custom crypto algorithms

##Code Analisys and Code Overview
**Core review rules:**
* all untrusted input is validated
* check error handling - close to source and handled consistently
* check for numeric overflows
* check crypto routines to make sure secrets are cleared quickly

**Look for the following vulnerabilities:**
* SQL injection
* Integer overflow
* Information disclosure: credetials, access to sensitive data, weak permisssions, crypto usages
* Race conditions
* Direct object reference: hard coded resources names, e.g. files' names
* Cross-site scripting
* URL redirection
* XPath injection

**Java code static anlysis tools:**
* findbugs 
* owasp lapse+ - part of eclipse
* visualcodegrepper
