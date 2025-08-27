# Stored XSS via SVG Upload openDCIM 23.04 https://opendcim.org/

## Description
A **stored Cross-Site Scripting (XSS)** vulnerability was identified in the image upload functionality of the application.  
Among the allowed file extensions, the `.svg` format is accepted. Since SVG is an XML-based format, it can contain embedded JavaScript code.  

We were able to inject a malicious script into an `.svg` file and upload it successfully. When this file is later viewed within the application, the JavaScript code executes in the client’s browser, resulting in a **stored XSS vulnerability**.

## Impact
- Execution of arbitrary scripts in the browsers of users viewing malicious SVG files.  
- Theft of cookies, session tokens, or other sensitive data.  
- Potential compromise of privileged accounts if an administrator views the malicious file.  

## Proof of Concept
After logging into the OpenDCIM application, a user can upload an .svg file via the following endpoint:
https://localhost/image_management.php
Template Management > Device Image Management > Select Files

![Alt text](/1.png)

![Alt text](/2.png)

content of test.svg
```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
   <polygon id="triangle" points="0,0 0,50 50,0" fill="#009900" stroke="#004400"/>
   <script type="text/javascript">
      alert("pwnandpatch");
   </script>
</svg>
```

Once uploaded, the image is accessible at:
https://localhost/assets/pictures/file.svg

![Alt text](/3.png)


## Remediation
**Restrict allowed file types:**  
- Block SVG uploads or any file formats that may contain executable code.  

**Sanitize uploaded files:**  
- Clean uploaded SVG files to remove any embedded JavaScript.  
- Disable script execution within SVG files.  

**Enforce security headers:**  
- Apply a strict [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) to limit unauthorized script execution.  

---

📌 *Severity: High*  AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:L/A:N
📌 *Category: Stored Cross-Site Scripting (XSS)*
