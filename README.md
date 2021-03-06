# ZeroSecrets 
ZeroSecrets is a Raspberry PI based image which implements a secure storage for your passwords, public and private key credentials and other secrets that 
are too hard to keep track of.

This image is designed to be used with a Raspberry PI Zero trust store device.  An example of such a device is shown below:

![Trust Store Device Picture](/doc/images/ZeroSecrets-Device.png)

ZeroSecrets devices are set up as USB Ethernet Gadgets.  A USB Ethernet Gadget is an ethernet device which plugs in to your USB port and can either be assigned an IP address which makes it part of your local network or it can be assigned an address which makes it a disconnected private internet connection that only the machine to which it is plugged in to may see.  The latter case is what we are looking for since it makes sure that no other devices on your network can access the USB device.  It should be noted that if someone has already rooted your laptop or desktop, then they will have a pathway to access the device so you should layer on defenses such as virus protection and anti malware for your PC for additional security.

By default the ZeroSecrets device uses Zero-configuration networking (zeroconf) which is a set of technologies that automatically creates a usable computer network based on the Internet Protocol Suite (TCP/IP) when computers or network peripherals are interconnected. It does not require manual operator intervention or special configuration servers. Without zeroconf, a network administrator must set up network services, such as Dynamic Host Configuration Protocol (DHCP) and Domain Name System (DNS), or configure each computer's network settings manually.

Zeroconf is built on three core technologies: automatic assignment of numeric network addresses for networked devices, automatic distribution and resolution of computer hostnames, and automatic location of network services, such as printing devices.[^1] 

What this means is that when you plug in a ZeroSecrets device into your computer your computer and the device will establish a miniature private network with an address in the 169.254.0.0/16 CIDR block.  Your ZeroSecrets device will get an address in that block (assigned at random) and will be visible to your computer only.  No other devices on your computers main network will be able to see the device, nor will the device be able to interact with any other device on your network.  From the ZeroSecrets perspective the only other computer on the network is the machine it is plugged in to.  From your networks perspective the ZeroSecrets device simply does not exist.  The advantage of this setup is that even if you are using a VPN such as BigIP edge and are logged in to a corporate network where the tunnel cuts off your own network, your ZeroSecrets device will still be visible to your machine which makes it possible to use this device on a corporate VPN without compromising corporate security policy since the device cannot see your PC or Laptops main network either and is cut off from the network. 

When properly configured the networking diagram looks something like this:

![Zero Secrets Network Diagram](/doc/images/ZeroSecretsNetworkDiagram.png)

The term properly configured applies to configuration of the ZeroSecrets device.  The above diagram shows a typical home network with multiple threat sources inside the firewall.

Even if the device were compromised somehow it would not be able to call back out to command control without the assistance of some sort of bridge installed by the hacker on your corporate laptop. Again, your endpoint security should be set up to protect your laptop from such an attack via corporate antivirus and other endpoint protection mechanisms.

So putting aside the issues of "What if my laptop is compromised by a hacker?" which is not the primary threats a ZeroSecrets device is intended to defend against, what does it defend against?

The answer is all of those folks out there trying to hack in to sites you use to steal your password and then see if they can use it to hack in to other accounts you use your password on...that's right we know what you are doing.  Sure everyone says you are supposed to be using cryptographically random password of sufficient complexity that no super computer would likely guess your password in the next 10 lifetimes but we don't do that do we?  What we do is come up with some easily guessable password such as **DiamondSushi777$** or such and then not only do we use that password on a public internet site, which is easy prey for a tool such as John The Ripper using the rockyou.txt file, but we use the same dang password on multiple sites!  Why do we do that?  It's because we can't keep track of a million or even a dozen long random passwords.  Our brains simply are not wired that way.  You can't write them down or someone could steal them from your trash or off your desk. You can't use a password keeper on your PC because we all know that those are quite frequently vulnerable as well![^2] 

The problem with your run of the mill password keeper is that it tightly integrates with your browser.  Even those that do not still store the passwords on your PC, and will load the unencrypted versions into memory.  If your PC is infected by malware then because your PC is the holder of the data and the keys it may be possible to compromise the password manager.  The other more dangerous vulnerability of normal password managers is the autofil capability.  This is a very bad idea from a security perspective and has been used to harvest not only the password of the site the victim is logged in to but ALL the passwords held by the password manager.  

So what do you do?  ZeroSecrets provides a path forward.  It is a simple device you can plug in to any laptop and access via a web browser.  You can use the UI to store secrets for different sites and then look them up later so that you can remember the password.  You can take it with you and use it on any of your PCs or laptops, and the data is secured on the device using a public private key or single password.  While you are back to using a memorable password your attack surface is greatly reduced.  Rather than having this easily guessable password on some server somewhere where the security of the password is beyond your control, it can stay in your head (hopefully) and to crack the password someone would need to have physical access to the SMD card in your ZeroSecrets device.  This makes it impossible for anyone who can't get up close and personal to you to get access to your secrets.  NOW you CAN use long hard to guess secure and unique passwords on every site you use and never have to worry about forgetting your password again (as long as you don't loose your ZeroSecrets device or it's SMD card that is).

ZeroSecrets keeps your passwords on what is essentially a separate computer which is not part of the primary network as your computer connects to, but is part of a small private network that only your computer and the device share.  You could enable sharing between your ZeroSecrets device and your main network but that would really defeat the purpose of the entire setup. 

ZeroSecrets presents a browser interface you can use to interact with the device.  For added security it is a good idea to use 2 browsers.  Use one brower (such as FireFox) to access your ZeroSecrets device, and use a separate browser to access the internet.  You can copy passwords from your ZeroSecrets browser into the web forms on your other browser.  Modern browsers do a great job of defending against attacks against the copy paste buffer and for a piece of Malware to escalate priveleges to the level where it can jump from one brower process to another is nearly impossible using modern OS with modern browsers.  It's not impossible but it is VERY unlikely.  But if the attacker has gotten that level of permission it's game over anyhow because the can just as easily monitor your keystrokes as you type in your password, so while it is a threat it is not as likely a threat as the more common attack vectors such as password stuffing, XSRF etc.

ZeroSecrets will generate cryptographically random passwords for you of any length you desire.  You can specify an added parameter which generates variable length passwords in a range of minimum to maximum characters to make the attackers job even harder.

Certain web sites may not allow you to paste a password into their web form.  This seems to be a common theme on municipal and state government websites and other organizations who don't bother to hire real security architects to flesh out their software engineering teams or at least to read [NIST Special Publication 800-63b](https://pages.nist.gov/800-63-3/sp800-63b.html) 

You can request ZeroSecrets to generate passwords in such a way as to enable you to easily type the password in by grouping alphanumeric charcters using a separator character.  The idea is that long random passwords make life hard for someone trying to do a dictionary attack.  In fact it makes dictionary attacks useless. You can change the grouping size for example groups of 4, 5, 6, 7, or 8 characters in a group.  The separator character is considered part of the password so you should choose a character that is acceptable to the web form validation. But when the password is displayed it is displayed in both a pasteable format and in an eye friendly format.  For examle:

    Length: 32; Grouping: 4; Separator: '-'; Case varies in group
    Pasteable: G8ib-Ym89-233X-59kx-vI8Z-WMAr-Q8h6-h7ty
    Eye Friendly: G8ib - Ym89 - 233X - 59kx - vI8Z - WMAr - Q8h6 - h7ty
    
    Length 21; Grouping: 7; Separator: ':'; Case consistent per group
    Pasteable: g8ibym8:9233X59:kxvi8zc
    Eye Friendly: t8ibym8 : 9233X59 : kxvi8zc
    
    Length 20-25; Grouping: 4,7; Separators: "-,#, %"; Case consistent per group
    Pasteable: GI5X-4VIY93C#6h6a212%3nmh
    Eye Friendly: YI5X - 4VIY93C # 6h6a212 % 3nmh
    
Case consistency per group makes it a little easier to type the password if you can't copy and paste by enabling you to toggle between upper and lower case with the CAPS-LOCK feature on your computer.
    

[^1]: https://en.wikipedia.org/wiki/Zero-configuration_networking
[^2]: https://www.welivesecurity.com/2020/03/19/security-flaws-found-in-popular-password-managers/
