# Revoked

## 1. Account Creation and Login
First, you need to create an account and log in:

![photo](https://github.com/t5nix/write-up/blob/main/assets/revoked.png?raw=true)

Once on the interface, the goal is to access the **Admin Panel**:

![photo](https://github.com/t5nix/write-up/blob/main/assets/admin.png?raw=true)

---

## 2. Analysis of the Search Bar
When arriving on the site, you notice a large search bar.  
I immediately check if it’s vulnerable (XSS, SQLi...), and bingo! By entering a fairly classic SQL injection, we can see that the bar is vulnerable:

![photo](https://github.com/t5nix/write-up/blob/main/assets/classic.png?raw=true)

But this vulnerability will not be directly useful yet.

---

## 3. Observing the JWT Cookie
Inspecting the cookies, we can see a **JWT token**:

![photo](https://github.com/t5nix/write-up/blob/main/assets/jwt.png?raw=true)

It’s our token, and we notice that `is_admin` is set to `0`.  
The goal is to change it to `1` to become an administrator:

![photo](https://github.com/t5nix/write-up/blob/main/assets/is_admin.png?raw=true)

Don’t forget to add `==` when decoding in Base64.  
However, the result is **invalid or revoked token**, so we deduce that we need to retrieve an employee token using the SQL injection discovered earlier.

---

## 4. Exploiting the SQL Injection
We inject the following payload:

' UNION SELECT id, token, 3, 4 FROM revoked_tokens --

This payload appends to the query result the `id` and `token` columns from the `revoked_tokens` table, filling the remaining fields with constants `3` and `4`, and ignoring the rest with `--`.

![photo](https://github.com/t5nix/write-up/blob/main/assets/token.png?raw=true))

And just like that, the employees’ tokens are visible.

---

## 5. Retrieving an Administrator Token
We simply find an **administrator token**:

![photo](https://github.com/t5nix/write-up/blob/main/assets/token_admin.png?raw=true)

Using it, we gain access to the **Admin Panel**:

![photo](https://github.com/t5nix/write-up/blob/main/assets/win.png?raw=true)

---

## 6. Flag
And that’s it, we obtain the flag:  
`Hero{N0t_th4t_r3v0k3d_ec6dcf0ae6ae239c4d630b2f5ccb51bb}`




