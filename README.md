<div align="center" >🤝 Show your support - give a ⭐️ if you liked the content | <a target="_blank" href='https://twitter.com/intent/tweet?url=https://github.com/Puliczek/CVE-2022-0337-PoC-Google-Chrome-Microsoft-Edge-Opera&text=CVE-2022-0337%20System%20environment%20variables%20leak%20on%20Google%20Chrome,%20Microsoft%20Edge%20and%20Opera&via=pulik_io'>SHARE on Twitter</a>
| Follow me on
 <a target="_blank" href='https://twitter.com/pulik_io'><img src='https://img.shields.io/badge/Twitter-%231DA1F2.svg?&style=flat&logo=twitter&logoColor=white'/></a>
 <a target="_blank" href='https://www.youtube.com/channel/UCaAdOBH2hnqLvEri1M7eg5Q'><img src='https://img.shields.io/badge/YouTube-%23FF0000.svg?&style=flat&logo=youtube&logoColor=white'/></a>
</div>

---

# **CVE-2022-0337 System environment variables leak on Google Chrome, Microsoft Edge and Opera**


# 📝 Description

Successful exploitation of this vulnerability can lead to the leak of user's secrets stored inside a system environment variables. A security bug was found in Chromium 92 version and patched in 97 version. There are several web browsers based on the chromium engine, for instance, Google Chrome, Microsoft Edge, Opera, and Brave. All of them were vulnerable, except for Brave. The vulnerability is in the File system access API, more specifically in window.showSaveFilePicker() method.

---

 CVE Version: **CVE-2022-0337**

 Credits: **Maciej Pulikowski**

 Reward: **$10,000 from Google VRP**

---

## Web browser versions vulnerability:
| [![Chrome](https://user-images.githubusercontent.com/12344862/159118096-8b37ffc9-f932-42cd-be6d-5f2034df11d6.png)](/) | [![Edge](https://user-images.githubusercontent.com/12344862/159118117-2cf623de-9dca-42af-87aa-be703c21511e.png)](/) | [![Opera](https://user-images.githubusercontent.com/12344862/159118118-9308dd8b-3b34-4bdb-a3c8-2a87af32d170.png)](/) | [![Firefox](https://user-images.githubusercontent.com/12344862/159118120-870fc0f1-1ab4-4594-b443-470efb4c7045.png)](/) | [![Brave](https://user-images.githubusercontent.com/12344862/159118121-97212f94-ec9f-4f64-95ca-6e87d9c12820.png)](/) |
| :-------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------: |
|                     92-96 Vulnerable ❗️❗️  <br> 97+ Patched (safe) ✔                     |                   92-96 Vulnerable ❗️❗️<br>97+ Patched (safe) ✔                    |                     78-82 Vulnerable ❗️❗️<br>82 Patched (safe) ✔                      |                                             Safe ✔                                              |                                          Safe ✔                                           |

Check your web-browser version:
- Google Chrome: `chrome://version/`
- Microsoft Edge: `edge://version/`
- Opera: `opera://update`


## Operation Systems:
**ONLY WINDOWS** are vulnerable. Linux and Mac are safe.

# 📺 Youtube Proof of Concept

https://www.youtube.com/watch?v=q7OIEWtalg8

[![PoC Video](https://user-images.githubusercontent.com/12344862/159117259-d4b9c0ad-fc25-45c6-aa68-580944f9c5a5.png)](https://www.youtube.com/watch?v=q7OIEWtalg8)

**Thanks for the thumbs up and subscriptions 😀👍**

# 👨‍💻 Code Proof of concept

## The simplest one
![proof of concept gif](https://user-images.githubusercontent.com/12344862/148587564-938eae98-eb6e-4e41-8de5-96ed21b368b9.gif)

1. Write in your Devtools:
```
let a = await window.showSaveFilePicker({suggestedName:'%username%'});
a.name;
```
2. Save file
3. Your username should appear

In the example, it is used a **%username%** environment variable. It returns Windows username logged in. Moreover, there are much more interesting environment variables:

Here are some examples:
- AWS_SECRET_ACCESS_KEY
- AZURE_CLIENT_SECRET
- binance_secret
- GITHUB_TOKEN
- GOOGLE_API_KEY
  
and many more...

So the attacker could gain an access to targets AWS services, Github account or Binance token to API (withdraw money) and more...

Check out more secrets in environment variables on my repository:

[🦄🔒 Awesome list of secrets in environment variables 🖥️](https://github.com/Puliczek/awesome-list-of-secrets-in-environment-variables)

[![🦄🔒 Awesome list of secrets in environment variables 🖥️](https://github-readme-stats.vercel.app/api/pin/?username=Puliczek&repo=awesome-list-of-secrets-in-environment-variables&theme=monokai)](https://github.com/Puliczek/awesome-list-of-secrets-in-environment-variables)

---

## Crafted HTML with improvlments
The previous example is run in local Google Chrome Devtools.

Obviously, the attacker could craft a special HTML file (website) to do successful exploitation of this vulnerability.

**env.html** - is an example of crafted special HTML in the repository.

But, do really user is required to download a file?

**Yes**, however, the attacker can create a website to encourage User to hold **ENTER** button on keyboard for 2 seconds to run and accept saving file dialog. Because windows dialog by default focus Save button, file will be saved with only blink.

![env example on website (1)](https://user-images.githubusercontent.com/12344862/148689723-ac0c670c-02b8-470d-8121-4da594612cfa.gif)
_*.gif is from Opera (Chrome and Edge were already fixed)_




In conclusion, user after holding the **ENTER** button on keyboard for 2 seconds could lead to leak of his system environments variables. This is a significant problem because user could store important secrets in system environments variable ex. Access to his AWS services, Github account or Binance 

# 🧠 Things to remember from the write-up:
- Environments variables can store secrets ex. tokens, api-key, secrets  
- if payload **"%USERNAME%"** or **"\$:USERNAME"** or **"\${USERNAME}"** or **"$USER"** return real username then target is vulnerable

# 🔨 Fix in chromium

I gave myself challange to repair the bug. Here, I would like to underline that I am not a c++ dev 😎.

source: https://bugs.chromium.org/p/chromium/issues/detail?id=1247389#c9

One of solution was to just replace character **%** in suggestedName to **_** . It should solve the problem.

### My solution:
![image](https://user-images.githubusercontent.com/12344862/148690850-3d21e54a-8922-433c-bf6a-298fea3c2bb8.png)

could be insert in:

https://source.chromium.org/chromium/chromium/src/+/main:content/browser/file_system_access/file_system_chooser.cc;l=238?q=ResolveSuggestedNameExtension&ss=chromium%2Fchromium%2Fsrc

and the results are:
## Before fix:

![image](https://user-images.githubusercontent.com/12344862/148690803-6a01664c-08a8-4626-a20c-57ae94c091ca.png)

## After my fix:

![image](https://user-images.githubusercontent.com/12344862/148690829-968da301-8aec-4383-a4d9-60af450bba8f.png)

Indeed it solved the problem, but it wasn't the best place to insert the code 😋 . However, I was very happy to manage to repair it by myself 😎

Final fix can be found here:

https://bugs.chromium.org/p/chromium/issues/detail?id=1247389#c12

# 🌌 Timeline

- [07.09.2021, 10:40 PM GMT+2] **Bug reported by Maciej Pulikowski**
- [07.09.2021, 11:36 PM GMT+2] **Bug accepted (Pri:1, Severity-High)**
- [08.11.2021, 1:30 AM GMT+1] **My submit for possible fix in code for chromium source**
- [10.12.2021, 6:09 PM GMT+1] **Fixed**
- [7.01.2022, 2:09 AM GMT+1] **Reward of $10,000**
- [18.03.2022, 6:30 PM GMT+1] **Google removed security view restrictions**

# 💻 Useful links

- [Issue 1247389: Security: Possible to see the user's system environment variables like secrets, tokens or keys](https://bugs.chromium.org/p/chromium/issues/detail?id=1247389)
- [🦄🔒 Awesome list of secrets in environment variables 🖥️](https://github.com/Puliczek/awesome-list-of-secrets-in-environment-variables)
- [The File System Access API: simplifying access to local files](https://web.dev/file-system-access/)
- [File System Access - Documentation](https://wicg.github.io/file-system-access/)
- [File System Access Web API - Chromium Security Model](https://docs.google.com/document/d/1NJFd-EWdUlQ7wVzjqcgXewqC5nzv_qII4OvlDtK6SE8)



# 🤝 Show your support

<div>🤝 Show your support - give a ⭐️ if you liked the content | <a target="_blank" href='https://twitter.com/intent/tweet?url=https://github.com/Puliczek/CVE-2022-0337-PoC-Google-Chrome-Microsoft-Edge-Opera&text=CVE-2022-0337%20System%20environment%20variables%20leak%20on%20Google%20Chrome,%20Microsoft%20Edge%20and%20Opera&via=pulik_io'>SHARE on Twitter</a>
| Follow me on
 <a target="_blank" href='https://twitter.com/pulik_io'><img src='https://img.shields.io/badge/Twitter-%231DA1F2.svg?&style=flat&logo=twitter&logoColor=white'/></a>
 <a target="_blank" href='https://www.youtube.com/channel/UCaAdOBH2hnqLvEri1M7eg5Q'><img src='https://img.shields.io/badge/YouTube-%23FF0000.svg?&style=flat&logo=youtube&logoColor=white'/></a>
</div>
<br>

# ✔️ Disclaimer
This project can only be used for educational purposes. Using this software against target systems without prior permission is illegal, and any damages from misuse of this software will not be the responsibility of the author.
