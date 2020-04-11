---
title: Randomly Generate Base62 for URL Shortening
categories: PROGRAMMING
date: 2020-04-11 22:23:49
tags: [base62, url shortening]
---
Last week I wrote a post about how to design an URL shortening service, like TinyURL, and came up with 2 strategies to encode an URL, and one of which is to randomly generate base64 sequence.

Although I knew it works, in theory, I was not so confident deep in mind.

So I wrote some code and made a simulation for generating 10^8 6-digit and 8-digit base62 sequences, respectively.

```cpp
struct Alphabet {
    std::array<char, 62> arr;
    constexpr Alphabet()
        : arr()
    {
        for (auto ch = 'a'; ch <= 'z'; ++ch) {
            arr[ch - 'a'] = ch;
        }

        for (auto ch = 'A'; ch <= 'Z'; ++ch) {
            arr[ch - 'A' + 26] = ch;
        }

        for (auto ch = '0'; ch <= '9'; ++ch) {
            arr[ch - '0' + 52] = ch;
        }
    }

    constexpr operator std::array<char, 62>()
    {
        return arr;
    }
};

constexpr std::array<char, 62> kAlphabet = Alphabet();

int main()
{
    std::default_random_engine engine(std::random_device{}());
    std::uniform_int_distribution<> dist(0, 61);

    std::unordered_set<std::string> keys;

    constexpr size_t kNum = 100000000;  // 10^8

    for (auto i = 0; i < kNum; ++i) {
        std::string key;
        key.reserve(8);
        for (auto k = 0; k < 8; ++k) {
            key.append(1, kAlphabet[dist(engine)]);
        }

        keys.insert(std::move(key));
    }

    std::cout << "keys-size=" << keys.size() << "; total=" << kNum << "\n"
              << "unique ratio=" << static_cast<double>(keys.size()) / kNum;

    return 0;
}
```

The result shows

- for generating 10^8 6-digit sequences, unique ratio of one-time generation is 0.9991
- for the 8-digit case, the unique ratio of one-time generation is high as 0.99999975

Even for 6-digit sequences, probability of two consecutive repeated generation is only 0.000001; and we can generate at least 1 million keys in less than 1 second.

Therefore, this strategy is indeed feasible in practice.

Notwithstanding, there is still one concernn we need to pay: the success ratio of one-time generation may drop dramatically after years, when number of generated combinations exceeds a threshould, like half of the entire combination space.

Unfortunately, I don't know what this threshould is in probability thoery; but if that day came, we can extend our sample space by extend length of base62 sequence, XD.
