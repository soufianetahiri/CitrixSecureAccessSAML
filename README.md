
# Why
The idea originates from a challenge I encountered. While remotely accessing a target user's computer, I found myself unable to connect to the VPN as that user due to the implementation of multi-factor authentication (MFA). The objective was to simulate a VPN connection on behalf of the user.

# Analyzing the thing
You might not know it but, all (or most of) the authentication workflow is handled by some JS files that are deployed with the installation.
Using a FileMon-like tool we can easily locate them, in my case, the JS was located here `C:\Users\soufiane.tahiri\AppData\Local\Citrix\AGEE\redacted_site_com\windows.VPN.environment.js` (redacted_site_com is the connection URL  that were in  use).

The JS is not obfuscated which made its analysis easy.
Upon analysis, we can easily locate a function called **onSamlCallback :**
![enter image description here](https://raw.githubusercontent.com/soufianetahiri/CitrixSecureAccessSAML/main/images/samlcallback.png)

If an attacker has access to a victim machine, It's easy to add a function to leak the SAML after a successful authentication:

![enter image description here](https://raw.githubusercontent.com/soufianetahiri/CitrixSecureAccessSAML/main/images/exfilsaml.png)
I've [attached a video](https://github.com/soufianetahiri/CitrixSecureAccessSAML/blob/main/images/citrix.mp4) demonstrating how this could be used. 

Once in possession of a valid SAML, an attacker can then introduce a new function to let the Citrix Client ask for a SAML like the following:

![enter image description here](https://raw.githubusercontent.com/soufianetahiri/CitrixSecureAccessSAML/main/images/samlusage.png)

I've [attached a video](https://github.com/soufianetahiri/CitrixSecureAccessSAML/blob/main/images/citrix2.mp4) demonstrating how this could be used. 

# Demo

![PoC](https://github.com/soufianetahiri/CitrixSecureAccessSAML/blob/main/images/citrix.gif?raw=true)
![PoC](https://github.com/soufianetahiri/CitrixSecureAccessSAML/blob/main/images/citrix2.gif?raw=true)

# Discloser


On Tue, Apr 16, 2024, 09:30 Cloud Software Group PSIRT <[secure@cloud.com](mailto:secure@cloud.com)> wrote:  

> Hello Soufiane,

> Our sincere apologies for the delay in responding. A few key members of our team were out of the office, resulting in a delay.
> We have thoroughly investigated your report, and based on our investigation, we acknowledge that it's possible for an attacker who has compromised a user's machine to use this access to gain access to SAML data and then use it for malicious intent. This scenario is actually a compromise of the end user's device, which creates the vulnerability and not the authentication workflow itself. An attacker who has already gained this level of access to a user's device would gain very little, if anything, from transferring the SAML data. As a result, we don't consider this to be a vulnerability within the product itself.  
>   
> We recommend that users take appropriate steps to protect their devices, such as restricting access and installing anti-malware software, as this would provide effective protection against an attacker gaining access to an end user's device and eventually stealing the cookie.
> We would like to thank you for your submission and for helping to keep Cloud Software Group's customers safe.  
> Regards,  
> Cloud Software Group Security Response Team
