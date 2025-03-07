# Sensitive Data Exposure
- Developers should encrypt data during transport and at rest, using the latest encryption algorithms
- Encrypt all data in transit with secure protocols such as TLS
- Dont store sensitive data unnecessarily
- Use a strong hashing or encryption algorithm where applicable
- when using a hashing algorithm be sure to use a salt and a pepper
- for data that doesnt need to be hashed, use symmetric encryption
- disable caching of sensitive data

Encryption: is the process of converting data or information into a scrambled, unreadable form known as ciphertext, using a secret key. It is a **two-way function**, so the original data can be recovered using the same encryption key.

Hashing: is the process of converting data or information into a fixed-length string of characters known as a hash value, using a mathematical function known as a hash algorithm. It is a **one-way function**, so it is not possible to recreate the original data from the hash value.

## Cryptographic hashes
These types of hashes are considered fast. They are not recommended for password protection because they are not designed to be resistant to attacks that are specifically tailored for cracking passwords. The main issue is that they are too fast, making it possible for an attacker to perform a large number of password guesses in a short amount of time. Additionally, algorithms like MD5 and SHA-1 are vulnerable to collisions where multiple inputs generate the same output, which also makes them unsuitable for password hashing.

Examples
MD5
SHA-1
SHA-2
SHA-3

## Password hashes
Password hashes are intentionally designed to be slow in order to make it more difficult for attackers to crack passwords by brute force or dictionary attacks. As computers and graphics processing units (GPUs) have become faster over time, it has become easier to perform massive brute force attacks against hashes on consumer hardware. This means that a hash can become less secure over time as it becomes easier to crack.

To address this issue, password hashing algorithms often include a "work factor". A work factor can be a single integer or multiple parameters that define the amount of computational effort required to calculate the hash. It can be adjusted to ensure that the hash remains secure despite advances in hardware.

In addition to the work factor, password hashing algorithms also use "constant time" performance to compute the hash of a password. This means that the time it takes to calculate the hash is the same regardless of the length or complexity of the password. Constant time performance is important in password hashing because it prevents attackers from using timing attacks to infer information about the password being hashed. Timing attacks involve measuring the time it takes for the password hashing function to execute and using this information to try to deduce the password or the input data.

Examples
Argon2
bcrypt
PBKDF2
scrypt


## Choose an appropriate password hashing algorithm
Below is the list of recommended password hashing algorithms:

Argon2: is the winner of the 2015 Password Hashing Competition. There are three different versions of the algorithm:
Argon2i: is optimized for resistance to side-channel attacks.
Argon2d: is optimized for resistance to GPU cracking attacks.
Argon2id: is a hybrid of the previous two that offers a balance of security and performance. Although it is slower than the other two, it is the recommended one.
bcrypt: it should be the second choice if Argon2id is not available.
PBKDF2: is recommended by NIST and has FIPS 140-2 validated implementations. So, it should be the preferred algorithm when these are required.


## Choose an appropriate configuration for Salting, Peppering and Work Factors
Salting: a salt is a unique and randomly generated string that is added to each password as part of the hashing process. Modern hashing algorithms such as Argon2id, bcrypt, and PBKDF2 automatically salt the passwords, so no additional steps are required when using them.
Peppering: a pepper is a secret added to a password hash like a salt. Unlike a salt, peppers are secret and the same pepper may be reused for many hashed passwords. They must not be stored alongside the hashed password. The purpose of the pepper is to prevent an attacker from being able to crack any of the hashes if they only have access to the database.
Work Factors: the work factor is essentially the number of iterations of the hashing algorithm that are performed for each password. The purpose of the work factor is to make calculating the hash more computationally expensive, which in turn reduces the speed and/or increases the cost for which an attacker can attempt to crack the password hash.
Argon2id: has three different parameters that can be configured to adjust the work factor. (m) minimum memory size, (t) minimum number of iterations, and (p) the degree of parallelism. The following configuration settings represent a base minimum:
Option 1: m=47104 (46 MiB), t=1, p=1
Option 2: m=19456 (19 MiB), t=2, p=1
bcrypt: the work factor should be as large as verification server performance will allow, with a minimum of 10.
PBKDF2: the work factor for PBKDF2 is implemented through an iteration count, which should be set differently based on the internal hashing algorithm used:
PBKDF2-HMAC-SHA1: 1,300,000 iterations
PBKDF2-HMAC-SHA256: 600,000 iterations
PBKDF2-HMAC-SHA512: 210,000 iterations


```csharp
using System.Security.Cryptography;
using System.Text;
using Isopoh.Cryptography.Argon2;
using Microsoft.AspNetCore.Identity;
using VikingBank.Data;

namespace VikingBank.Utilities;

public class Argon2PasswordHasher<TUser> : IPasswordHasher<TUser> where TUser : User
{
    public string HashPassword(TUser user, string password)
    {
        var config = new Argon2Config();
        config.Type = Argon2Type.HybridAddressing;
        config.Version = Argon2Version.Nineteen;
        config.Password = Encoding.UTF8.GetBytes(password);
        config.Salt = GenerateSalt();
        config.MemoryCost = 15360;
        config.TimeCost = 2;
        config.Lanes = 1;

        using (var argonPassword = new Argon2(config))
        {
            using (var hash = argonPassword.Hash())
            {
                return config.EncodeString(hash.Buffer);
            };
        };
    }
    
    public PasswordVerificationResult VerifyHashedPassword(TUser user, string hashedPassword, string providedPassword)
    {
        return Argon2.Verify(hashedPassword, providedPassword) ?
            PasswordVerificationResult.Success : PasswordVerificationResult.Failed;
    }
    
    public byte[] GenerateSalt()
    {
        var salt = new byte[32];
        RandomNumberGenerator.Fill(salt);
        return salt;
    }
}
```
---
