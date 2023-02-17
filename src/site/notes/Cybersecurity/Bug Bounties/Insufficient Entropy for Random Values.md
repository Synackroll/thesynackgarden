---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounties/insufficient-entropy-for-random-values/"}
---

From [this](https://phpsecurity.readthedocs.io/en/latest/Insufficient-Entropy-For-Random-Values.html) page.

Random values have important uses:
* Select randomly from a pool of known options.
* Generate ungessable tokens or nonces for authorization.
* Generate initilaisation vectors for encryption.
* Generate unique identifiers like Session IDs

If an attacker can guess or predict the output of an random number generator #RNG or Psuedo-Random Number Generator #PRNG, they will be able to correctly guess tokens, salts, nonces, and cryptographic iniitilisation vectors created usingthe generator.

Two potential vulnerabilities stem from random values in PHP:
1. Information Disclosure
2. Insufficient entropy.

The factor that makes a random value strong is the entropy used to generate it. Entropy is just the measure of uncertainty.

mt_rand() from PHP generates random values which are **always** digits. mt_rand() also is not a true random number generator. It is a PRNG or Deterministic Random Bit Generator #DRBG that implements an algorithm called Mersenne Twister (MT) which approximates random numbers.

If you have the seed for any particular run of the MT then you have all of the numbers it will generate too. Thus, for any pariticular 

## Random Values In PHP[](https://phpsecurity.readthedocs.io/en/latest/Insufficient-Entropy-For-Random-Values.html#random-values-in-php "Permalink to this headline")

PHP uses three PRNGs throughout the language and both produce predictable output if an attacker can get hold of the random value used as the seed in their algorithms.

1.  Linear Congruential Generator (LCG), e.g. lcg_value()
2.  The Marsenne-Twister algorithm, e.g. mt_rand()
3.  Locally supported C function, i.e. rand()

## Attacking PHP's Random Number Generators

### Vulnerable application Characteristics

#### 1. The server uses mod_php allowing multiple requests to be served by the same PHP process when using KeepAlive

#### 2. The server exposes CSRF, password reset, or account confirmation tokens generated using mt_rand() based tokens.

In this case we can derive a seed value by directly inspecting a number generated by PHP's random number generators. We can source this from any value we can access as long as it is the using the same process as the secon token we're trying to predict.

#### 3. Known weak token generation algorithm.

If you can source the generation algorithm from a disgruntled employee etc.

Overall attack:
* Get a token from the process you're trying to generate another token from.
* Crack the SHA512 hash of the token to get the random number generated originally by the server.
* Use the random value to brute force the seed value.
* Use the seed to generate a series of random values likely to have been the basis of the password reset token.
* Use a password reset token to reset the Administrator's password.

Generate token with this code:
```php
$rand = mt_rand();
echo "Random Number: ", $rand, PHP_EOL;
$token = hash('sha512', $rand);
echo "Token: ", $token, PHP_EOL;

Token: 53bfefe79fc86e2f210ffab7cc4e02156b47fab53904c36cc5fe5d039d759f90919e7f76ee27142e4bba6d199339f72e8837f455b7afef68e59caab43ffe113
```

./oclHashcat-lite64 -m1700 --pw-min=1 --pw-max=10 -1?d -o ./seed.txt <SHA512 Hash> ?d?d?d?d?d?d?d?d?d?d