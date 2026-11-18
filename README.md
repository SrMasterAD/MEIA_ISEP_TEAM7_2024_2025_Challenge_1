<div align="center">

# PitStop

An expert system that helps a driver find out what is wrong with their car. It asks about the noises, smells, smoke and dashboard lights they notice, then names the most likely fault, how sure it is, and which answers led there.

![Java](https://img.shields.io/badge/Java-17-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-3-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)
![Drools](https://img.shields.io/badge/Drools-8-CC0000?style=for-the-badge&logoColor=white)
![Prolog](https://img.shields.io/badge/Prolog-SWI-74283C?style=for-the-badge&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-Express-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

[Objectives](#objectives) · [Features](#features) · [Architecture](#architecture) · [How it reasons](#how-it-reasons) · [Team](#team)

<sub>Challenge 1 · Master's in Artificial Intelligence Engineering · <a href="https://www.isep.ipp.pt">ISEP</a>, Porto · 2024–25</sub>

</div>


## Objectives

Turn a mechanic's knowledge about car faults into an expert system that diagnoses them and explains its answers. These were the goals:

- [x] Interview a mechanic about how they find engine, exhaust, fuel and starting faults
- [x] Map what we learned into a flow of questions, answers and conclusions, then program it as rules
- [x] Ask the driver only the questions that matter, one at a time, based on what they already answered
- [x] Give each diagnosis a confidence level and explain how it was reached
- [x] Build the same knowledge base twice, once in a production rules engine (Drools) and once in Prolog, so the two approaches can be compared



## Features

| | Feature | What it does |
|:-:| --- | --- |
| 🔧 | **Guided questions** | Asks about engine noises, smells, smoke, dashboard lights and trouble starting, one question at a time; some questions accept several answers |
| 🎯 | **Diagnosis with confidence** | Names the likely fault out of 14, such as a blocked catalytic converter or a burnt head gasket, with how sure it is (from 50% to 100%), and lets you browse when more than one fits |
| ❓ | **"How" explanation** | Lists each rule that fired and the answer behind it, so you can follow the reasoning step by step |
| 🔀 | **Two engines** | Pick Drools or Prolog on the start screen; both ask the same questions and reach the same diagnoses |

## Architecture

The web page talks to whichever engine you pick, and each engine runs as its own small server:

```
Web page (JavaScript, Express)  →  Drools engine (Java, Spring Boot REST API)
                                →  Prolog engine (SWI-Prolog HTTP server)
```

Both engines offer the same two calls: one to start a diagnosis and one to send the next answer. When a rule needs to know something, the engine sends a question back to the page and waits for the answer before it carries on. In Java the Drools session runs on its own thread, which pauses until the answer arrives through the REST API. In Prolog the engine keeps the confirmed symptoms as facts and checks the rules again after every answer.

The page itself is plain HTML, CSS and JavaScript served by Express, and uses Axios to call the engines.

## How it reasons

The knowledge base has 149 rules, written once for Drools (`rules.drl`) and once in Prolog with custom `rule … if … then …` operators. A rule says which earlier answers it depends on and what to do next: ask a follow-up question, skip a branch that no longer applies, or conclude a diagnosis. Starting from the first question ("which symptoms does your car show?"), each answer only opens the rules that build on it, so the driver never sees questions about faults already ruled out.

Diagnoses carry a confidence level set by the rule that reaches them. The same fault can be reached through different paths, and a path with fewer or weaker signs gives a lower confidence, for example a blocked catalytic converter at 60% instead of 100%.

For the "How" explanation, both engines record every rule that fired together with the question and answer that triggered it. When a diagnosis is reached, that trail is attached to it, and the page shows it as a table next to the result.

## Project structure

```
Backend/
├── src/main/java/
│   ├── API/                  Spring Boot app, REST controller and DTOs
│   ├── Engine/               Drools session runner and the "How" explanation
│   ├── fabrica/              asks the page a question and waits for the answer
│   └── model/                symptoms, diagnoses and facts used by the rules
├── src/main/resources/       Drools rules (rules.drl) and session config
├── src/PROLOG/               Prolog engine, its HTTP server and the rules
└── src/test/postman/         Postman requests for the Drools API
Frontend/                     web page, styles, images and the Express server
```

> The questions, rules and diagnoses are written in Portuguese.

## Team

<table align="center">
  <tr>
    <td align="center" width="150">
      <a href="https://github.com/Marco-Nobrega-Andrade"><img src="https://github.com/Marco-Nobrega-Andrade.png?size=200" width="100" alt="Marco Andrade"/></a><br>
      <b>Marco Andrade</b><br>
      <a href="https://github.com/Marco-Nobrega-Andrade">@Marco-Nobrega-Andrade</a>
    </td>
    <td align="center" width="150">
      <a href="https://github.com/SrMasterAD"><img src="https://github.com/SrMasterAD.png?size=200" width="100" alt="David Dias"/></a><br>
      <b>David Dias</b><br>
      <a href="https://github.com/SrMasterAD">@SrMasterAD</a>
    </td>
    <td align="center" width="150">
      <a href="https://github.com/1200833Rui"><img src="https://github.com/1200833Rui.png?size=200" width="100" alt="Rui Ribeiro"/></a><br>
      <b>Rui Ribeiro</b><br>
      <a href="https://github.com/1200833Rui">@1200833Rui</a>
    </td>
    <td align="center" width="150">
      <a href="https://github.com/HS2000PT"><img src="https://github.com/HS2000PT.png?size=200" width="100" alt="Henrique Santos"/></a><br>
      <b>Henrique Santos</b><br>
      <a href="https://github.com/HS2000PT">@HS2000PT</a>
    </td>
  </tr>
</table>
