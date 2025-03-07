
# Security Misconfiguration
- Using known vulnerable components 
# Insufficiently Protected Credentials

# Weak Algorithm Cryptography

- Rainbow Tables

## TLS
- update TLS to latest version
- enable GCM ciphers onlye (if possible)
- always disable NULL, Anonymous or EXPORT ciphers
- Avoid outdated algorithms like MD5 and SHA-1
- Disable TLS compression

# Trojan Crazyness

The vulnerability illustrated is called a Trojan Source commenting-out attack, where Unicode override control characters are hidden in comments causing the logic of the code rendered in the IDE, to be different from that of the compiling code.

```
A computer might render:
    /*‮ } ⁦if (isAdmin)⁩ ⁦ begin admins only */
    return "you are admin";
    /* end admins only \‮ { ⁦*/

But the file actually contains this:
    /*RLO } LRIif (isAdmin)PDI LRI begin admins only */
    return "you are admin";
    /* end admin only RLO { LRI*/
```

Notice how the admin check gets completely commented out, the way the compiler/interpreter reads the source. RLO, LRI, PDI, etc. are the control characters which determine the text direction.

The possibility of a **Trojan Source vulnerability** entering the code base is not unlikely. Such code could be naively copy-pasted from samples on the internet, or it could perhaps be introduced through using open source frameworks or libraries, where malicious code contributions went unnoticed. This, in combination with an IDE or text editor which does not display or mention the presence of override control characters, could lead to your codebase being compromised.

94923054