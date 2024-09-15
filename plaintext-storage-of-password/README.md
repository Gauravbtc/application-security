# Plaintext Storage of Password

The plain text storage of passwords is a vulnerability related to improper storage of sensitive information. This practice can lead to significant security risks.

## Introduction
Plaintext storage of passwords refers to the practice of storing passwords exactly as the user entered them without any other security measures in place. This is a common practice, but it is generally considered to be a poor security practice, as it makes it easy for anyone who gains access to the password database to see and use the passwords.

## Description
Whenever an application has to deal with password storage, it is essential to avoid plaintext format and choose the appropriate protection level. The majority of modern languages and frameworks provide built-in functionality to help store passwords safely. However, there are two important concepts to take into account when protecting passwords:

- **Encryption**: The process of converting data or information into a scrambled, unreadable form known as ciphertext, using a secret key. It is a two-way function, so the original data can be recovered using the same encryption key.
  
- **Hashing**: The process of converting data or information into a fixed-length string of characters known as a hash value, using a mathematical function known as a hash algorithm. It is a one-way function, so it is not possible to recreate the original data from the hash value.

When dealing with password protection at rest, best practices suggest using the hashing technique. This way, once the password's hash values are stored, the original data cannot be recreated in case of a datastore compromise. Password encryption is only recommended in some edge cases where it is necessary to obtain the original plaintext password.

## Types of Hashing

### 1. Cryptographic Hashes
These types of hashes are considered fast. They are not recommended for password protection because they are not designed to be resistant to attacks that are specifically tailored for cracking passwords. The main issue is that they are too fast, making it possible for an attacker to perform a large number of password guesses in a short amount of time.

**Examples:**
- MD-5
- SHA-1
- SHA-2
- SHA-3

### 2. Password Hashes
Password hashes are intentionally designed to be slow in order to make it more difficult for attackers to crack passwords by brute force or dictionary attacks. As computers and graphics processing units (GPUs) have become faster over time, it has become easier to perform massive brute force attacks against hashes on consumer hardware. This means that a hash can become less secure over time as it becomes easier to crack.

To address this issue, password hashing algorithms often include a "work factor". A work factor can be a single integer or multiple parameters that define the amount of computational effort required to calculate the hash. It can be adjusted to ensure that the hash remains secure despite advances in hardware.

**Examples:**
- bcrypt
- PBKDF2
- scrypt

## Mitigation Techniques

- **Use password hashing instead of password encryption**: Passwords must be protected at rest using a password hashing technique. This way, if the underlying datastore is compromised, the original passwords would not be exposed. If passwords are protected using an encryption technique, if an attacker manages to steal the encryption key and compromise the password datastore, the original passwords would be exposed.

- **Select a suitable password hashing algorithm** such as bcrypt or Argon2.

- **Choose an appropriate configuration for Salting, Peppering, and Work Factors**.
  - **Salting**: A process of adding a unique, random value (called a "salt") to each password before hashing it. This ensures that even if two users have the same password, their hashed values will be different.
  - **Peppering**: It is similar to salting but adds an additional secret value (called a "pepper") to the password, which is not stored in the database. Instead, the pepper is often hard-coded into the application or stored in a separate, highly secure location.
  - **Work factor**: A parameter that controls how computationally expensive the hashing process is. In the context of password hashing algorithms like bcrypt, Argon2, or PBKDF2, the work factor determines the number of iterations the algorithm goes through to generate the hash.

## Pseudocode

### Unsecure Code Example
```plaintext
-- POST "/registration"
Define method UserRegistration with argument request
    Fetch username from body of request
    Fetch password from body of request

    -- Input validation code omitted on purpose
    -- "username" parameter validation
    -- "password" parameter validation against password policy

    -- Store the raw password directly in the database (Unsecure)
    Call database.SaveUser with arguments (username, password)
    Save it as resultOK

    -- Rest of code omitted on purpose

End method

###Secure Code Example 
```plaintext
import Bcrypt from ExternalCryptoLibrary

-- POST "/registration"
Define method UserRegistration with argument request
    Fetch username from body of request
    Fetch password from body of request

    -- Input validation code omitted on purpose
    -- "username" parameter validation
    -- "password" parameter validation against password policy

    -- Use Bcrypt to generate a password hash with salting and work factor
    Call Bcrypt.GeneratePasswordHash with argument password
    Save it as passwordHash

    -- Store the hashed password in the database (Secure)
    Call database.SaveUser with arguments (username, passwordHash)
    Save it as resultOK

    -- Rest of code omitted on purpose

End method
