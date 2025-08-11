---
timezone: UTC+8
---

# kylin

**GitHub ID:** JacksonStack

**Telegram:** @kylinzo

## Self-introduction

developer rust layer2

## Notes

<!-- Content_START -->
# 2025-08-11

> ref: https://web3intern.xyz/zh/smart-contract-development/

#### 1 dapp

Dapp = UI + Smart-contract + Indexer + Blockchain/Decentralized-storage  

##### Architecture  
• UI: React/Vue/HTML-CSS-JS, talks to chain via wallet (MetaMask).  
• Smart-contract: Solidity on EVM, stores state & emits events.  
• Indexer: Listens to events (e.g. Transfer), writes to Postgres/GraphQL so UI can query lists not directly available on chain.  
• Storage: on-chain for state, IPFS/Arweave for large files; guarantees persistence & decentralization.

##### Development workflow  

1. Planning: define features, pick chain (ETH/Solana/Polygon), design UX.  
2. Contract: write (Solidity), unit-test, audit, optimize.  
3. Indexer: choose Ponder/Subgraph (TypeScript), map events to DB, Docker/SaaS deploy.  
4. Front-end: React/Vue, plug wallet, fetch data from indexer & chain via Viem/Ethers/Wagmi, handle tx signing & confirmations.  
5. Deploy: contract with Hardhat/Foundry to testnet/mainnet; UI to IPFS/Vercel; monitor, update, maintain.

Key challenges: user experience, gas costs, security, real-time data, cross-framework tooling.

#### 2 env

##### basic 

node.js (a js runtime)   use nvm to control the version

npm or yarn (package management)

git 

##### local dev chain

- foundry (rust)     wow, now have to take a try
- hardhat 

##### interaction between wallet and frontend

- metamask    plugin wallet
- frontend       viem or wagmi 

##### others

- remix ide (web)
- openzeppin      smart contract lib
- Chainlink 

#### 3 Solidity

well I choose  somewhere else to learn this

And I've read a lot of blogs or articles about how to learn a pl quickly  

and the answer is to learn the pl's features.  

> here is one of the blogs 
> ref https://www.yinwang.org/blog-cn/2017/07/06/master-pl

after learning one or two or three pls, you may notice these are what you need to learn  

variables, arithmatic ops,  if/else, for/while/loop, function, struct or class, file/io, advanced data structures, algorithms, lib....
more or less, but something similar  
then you may got something like programming paradigm  

........

# 2025-08-10

> nothing particular, just write something  
> ref:  
> - [Thinking & Writing](https://batsov.com/articles/2022/05/26/thinking-writing/)  
> - [An interview with Leslie Lamport](https://mihaibudiu.github.io/blog/lamport.html)  
> *before you code, you need to think. And if you want to think, you need to write. Otherwise, you are just thinking that you are thinking.*  
> that’s what I got after listening to some shares. Below is the original version.  
> If you’re thinking without writing, you only think you’re thinking – Leslie Lamport  
> And things about this scientist, read [Vitalik’s tweet](https://x.com/VitalikButerin/status/1027972126593015809) and [A Guide to 99% Fault Tolerant Consensus](https://vitalik.eth.limo/general/2018/08/07/99_fault_tolerant.html); that will give you something about what he did.  
> And one more quote about writing.  
> Writing about your ideas both exposes flaws in them and develops them further. Which means thinking without writing = having incorrect and incomplete ideas. – Paul Graham  
> This one is not going to be a strict tech blog; it’s more of an exercise now. The more you write, the more formal your writing will become.

#### programming languages

So, write something on programming languages.  
I’ve known a bunch of PLs: C++, C, Java, H5, C3, JS, Python, Go, Rust, Haskell, Clojure, Lisp (Scheme), Elixir, Zig, Solidity, Move, and I’ve heard of many more—Ada, Fortran, OCaml, Erlang, COBOL, Perl, Ruby, R, Julia, C#, Lua, VB, BASIC, HDL (VHDL, Verilog). No need to mention the tech stacks built on top of them.  

I can’t say I’ve mastered any of them.  

Which reminds me that many programmers with a lot of programming, work, or problem-solving experience say the programming languages do not matter; they are just tools to solve problems, and the ability to understand and solve problems is far more important.  

Right now, I hold a different opinion. I may not fully grasp what they’re trying to say, but since almost every job description lists one or more specific PLs, I have the number-one piece of evidence to support my perspective.  

Furthermore, I’d stress something lies behind a PL and that usually comes down to its underlying philosophy. Of course, every PL is implemented to solve one or more kinds of problems. You may see this supports for the "not matter perspective", while I can also say this supports my opinion.

#### directions (to be continued)

# 2025-08-09

> ref https://web3intern.xyz/zh/bruce-hiring-perspective/
> How to Be a Reliable Web3 Intern (Bruce’s Perspective)  
> Exactly what I need
  
1. Definition of Reliable  
   Predictable (deliver when promised) + Communicative (speak up early when blocked) + Reflective (post-mortem & improve).

2. Time Estimation – “Experience Multiplier”  
   Multiply your gut estimate by 1.25–2× depending on familiarity; break tasks into micro-steps; add 10–15 % buffer; pre-align with mentor.

3. Classic Unreliable Behaviors  
   Ghosting, perpetual “it’ll be done tomorrow,” and silent solo grinding until deadline.

4. Communication Cadence  
   First weeks: daily 3-line update (what I did / stuck on / plan). After that, ping the team within 30 min of being stuck, but always try self-troubleshooting first.

5. Balancing Depth vs Delivery  
   Write a 30-min mini-plan or flowchart, then iterate in layers: core feature → polish. Never sacrifice the hard deadline.

6. Key Soft Skills & How to Test  
   - Clear communication – watch structured storytelling.  
   - Deep thinking – ask successive “why” questions.    this reminds me the classic blog " How To Ask Questions The Right Way"
   - Reflection – give small challenges and see if they self-review and adapt.

7. Coaching When Slipping  
   Ask intern to describe problem & attempted fixes; set a one-week improvement window. Repeated identical mistakes = attitude issue; varied mistakes that are quickly fixed = skill gap.

8. Final Advice  
   Web3 rewards openness; reliability multiplies learning speed.

# 2025-08-08

> watch the two replays
- first one, 
- second one, is about laws and limitations
    - Common charges
        - fundraising fraud
        - illegally absorbing public deposits
        - organize and lead pyramid scheme activities
        - open a casino
        - illegal business operations

# 2025-08-07

> the notes of 05/08/25
> ref: https://web3intern.xyz/zh/position-introduction/

*if you don't know what to learn to gain a job, take a look at the jb(job description) to know the requirements*  

-    so basically, just the old school way with some extra things related to the chain  
     - frontend  the basic h5/c3/js or ts and a framework vue or react(nextjs in particularly) and something to the chain (ether.js/web3.js old time, viem now)  
     - backend  
        - Java(Spring...) / Python(Django/flask/fastapi) / Nodejs(nestjs) / Go(gin/beego/...)  the web framework   
        -  db tech and how to interact with them: mysql/ pgsql/ redis/ mongodb/....
        - api    restful graphql rpc
        - message queue    rabbitmq/rocketmq/kafka
        - cloud    docker/kubernets
        - ci/cd  gitlab
    - smart contract   
        - nft   erc standards...
        - go to hackquest to know more, like solidity for eth, rust for arb and sol, Move for sui  ...
- for other jobs    it does not mean these jobs don't need any tech skills, many of them require the ability of data analysis, wtf...   excel, r/julia/python/sql/bi.. even spark...

# 2025-08-05

> ref: https://web3intern.xyz/zh/security/  

laws and limitations  
- Web3 jobs often lack Chinese labor contracts, social insurance, and pay in tokens—risking unpaid wages, frozen funds, and legal trouble. Vet projects, demand compliant pay, and avoid shady crypto cash-outs.  

cybersecurity  
- risks and attack methods
    -  phishing 
        - forge official websites, social accounts, emails and text messages, induce victims to click on malicious links and enter sensitive information (such as mnemonics, private keys and account passwords) , or download malicious software.
    - malware & trojan
        - masquerading as interview/learning software  
        - clipboard hijacking
        - backdoor browser extension/plug-in 
        - remote control
   - social engineering
        - impersonate HR/mentor/classmate
        - catfish 
        - stolen accounts
    - supply chain/third party dependency attacks
    - clipper/scanning bots
    - traditional privacy and account security risks
- how to avoid: official Channel + dual authentication + cold storage private key + address verification  
my own experience  
- MySQL server deployed in cloud server, due to simple password, was hacked and they asked for some btc


# 2025.08.02


<!-- Content_END -->
