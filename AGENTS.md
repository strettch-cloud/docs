# Working in this repo

Instructions for any coding agent. People who write pages here follow the same rules.

## This repository is public

Anyone can read everything here: the pages, the OpenAPI file, commit messages, branch names, pull request titles and descriptions, review comments, and every earlier version of a pull request description.

Write only what a customer needs to use Strettch Cloud. Never put any of these anywhere in this repo or its pull requests:

- internal ticket numbers or titles, or links to private repositories, pull requests, documents or dashboards;
- names of internal services, flags, fields, hosts, IP addresses or infrastructure;
- how a feature is built, tested or rolled out, or what isn't finished yet;
- anything that only makes sense to the Strettch Cloud team.

Describe behaviour as a customer sees it. If a page can be published only once something ships, keep its pull request as a draft and leave the reason out of it. When unsure whether something is internal, leave it out and ask.

Removing a mistake later doesn't make it private again. An edited pull request description keeps its earlier versions, and renaming the branch of an open pull request closes it. Get it right before you push.

# Writing a page

These rules apply to every page in this repo, whoever writes it. Read them before you write or edit a page.

## The key rule: write the way you would say it to a customer

Keep a human tone. Write the way you would explain it to a colleague sitting next to you. A human tone does not mean chatty or funny. It means plain, direct and honest.

How to check: read the sentence aloud. If you would not say it to a customer on a call, rewrite it.

- **Start with the answer.** The first sentence of a page or section says the thing. Background comes after.
- **Say "you" and "we".** Present tense, active voice. "You pay for traffic above the allowance", not "charges are incurred".
- **One idea per sentence.** Use short, common words. Some readers use English as a second or third language, so avoid idioms.
- **Use facts, not praise.** Give the number, the limit or the button name. Leave out words that only sell: seamless, robust, enterprise-grade, lightweight, powerful, comprehensive, perfectly, virtually.
- **Do not tell the reader it is easy.** Drop "simply", "just" and "easily". A reader who is stuck does not find it easy.
- **Be honest about limits.** Say what does not work and what costs money in the same plain voice. "A stopped compute is still billed."
- **No template labels.** Do not open sections with "Problem:" or "Why it matters:". Say it in a normal sentence.
- **No decoration.** No exclamation marks and no emoji in instructions. Bold is for screen names only.

Three examples:

| Instead of | Write |
|---|---|
| "SSH keys are a robust and secure way to log into your Strettch Cloud compute instances without typing a password." | "An SSH key lets you log in to a compute without typing a password." |
| "**Problem**: How do I control when my compute instance is running or stopped?" | "You can turn a compute off and on from its page. A compute that is off is still billed." |
| "It provides enterprise-grade performance with fast propagation across the global network." | "A record change takes effect on our nameservers immediately. Other resolvers may keep the old value until its TTL runs out, 60 seconds by default." |

The Bandwidth page is the model for tone. Example: "Stopped computes still count. A stopped compute is still billed, so it keeps adding to the allowance."

## Rules for every page

1. **One fact lives on one page.** A price, limit, time, port or permission is explained on the page that owns it. Another page may repeat it in one sentence, with a link, when the reader needs it to act safely there: a cost, downtime, or something that cannot be undone. The table further down says which page owns which fact.
2. **One name per thing.** Use the glossary below. Do not switch words for variety.
3. **Use the exact words on the screen.** Put button, tab and menu names in bold, spelled and capitalised as the product does: **New Compute**, **Team Settings**, **Destroy Compute**. A step title that is a screen name keeps the screen's capitals.
4. **Describe a choice. Do not list what is in it.** See the section below.
5. **Give the number when the reader needs it and no screen shows it.** An honest approximate time is a fact, so keep it: "about a minute". Do not put "fast" or "affordable" where a number belongs.
6. **Say the cost before the step.** If a step costs money, causes downtime or cannot be undone, say so before the reader does it.
7. **At most two callout boxes per page.** A warning box is for three things: getting locked out, losing data, and a charge the reader would not expect, such as billing that continues after a compute is turned off. If the page has one of these, it gets a box. A normal price, such as a public IP being billed separately, is a plain sentence.
8. **Document what works today.** No "coming soon", "on our roadmap" or "in the future". No explanation of how we built it or what our providers do. See "This repository is public" above.
9. **Do not teach what is not ours.** No general advice on firewalls, caching or database tuning. Link to the tool's own docs.
10. **Headings say what the section answers.** Sentence case: "How the team allowance works". No heading that is only a label, like "Overview" on a page already titled Overview.
11. **One placeholder style.** `YOUR_COMPUTE_IP` in commands you copy. Example addresses come from the documentation range `192.0.2.x`.
12. **No standard footer.** Do not end pages with a "Need help?" box; Support is in the menu. Add "Next steps" only when there is a real next step, with three links at most.
13. **Keep old addresses working.** When you split, move or rename a page, add a redirect from the old address and fix the links that pointed to it.
14. **Run it before you publish it.** Follow your own steps on a real compute, with a normal customer account, and fix what does not match. If you cannot run the steps or see the screen, use the glossary's button names where it has one, keep the page's existing wording for every other button and step, and hand back a list of every button name, step and value you could not check.

### Describe a choice, do not list what is in it

Lists of options, images, versions, plans and prices change in the product without anyone updating the docs. The page then states something false.

If the reader will see the values on the screen at the moment they choose, say what the choice is and how to choose. Do not copy the values.

| Do not write | Write |
|---|---|
| "Choose an expiry of 7 days, 30 days or 90 days." | "Choose how long the key stays valid. A shorter period limits the damage if the key leaks." |
| "We offer Ubuntu 22 LTS, Ubuntu 24 LTS, Debian 12 and Debian 13." | "Choose an image. The list shows the Linux versions and application images you can use." |
| "Pricing starts at 3,999 RWF per month." | "The price of each specification is shown when you create a compute." |

The check: would this sentence be wrong on the day we add or remove one option? If yes, rewrite it.

State a value only when both of these are true: the reader needs it to act, and no screen shows it to them at that moment. A port number, a rate limit and a default are examples. The value then lives on one page (rule 1).

Exact lists of allowed values belong on reference pages, generated from the source where we can.

A list stays when the page explains each item and the screen does not. The table of compute graphs is an example: the screen shows the graphs, and the page says what to watch for in each one.

Two more things go stale the same way:

- **Screenshots.** Use one only when words cannot show where something is.
- **Sample command output.** Keep only the lines the reader must recognise, and leave out version numbers and dates.

## Rules by page type

Decide the type before writing. One page is one type.

| Type | The reader is asking | Model page |
|---|---|---|
| Concept | How does this work, and what does it mean for my bill or my setup? | Bandwidth |
| How-to | How do I do this one thing? | Receipts, "Generate a receipt" |
| Quickstart | What is the shortest way to a working result? | Terraform Quickstart |
| Tutorial | Can you walk me through building something real? | Deploy a web server |
| Application image | What is on this image and how do I start it? | Coolify |
| Reference | What is the exact value, field or code? | API Introduction |
| Troubleshooting | It is broken. What do I check? | SC Agent, "Console not accessible?" |
| FAQ | A short question with a short answer | Bandwidth FAQ |

When a page sits between two types: if it explains what something means, it is a concept page. If it lists exact values to look up, it is reference.

### Concept

- Open with two sentences that give the whole idea. A reader who stops there should still be right.
- One rule per section. State the rule. If the rule involves numbers, add one worked example with real ones.
- Say where the reader can see it in the dashboard.
- No steps. Link to the how-to.

### How-to

- The title is the task: "Upsize a compute", "Add an SSH key to a team".
- Before the steps, say what the reader needs to start: a role, enough credits or funds, a public IP. If nothing is needed, start with the steps. Do not open by announcing what the page will do. The title already says it.
- Numbered steps that follow the screen. If the screen has six steps, the page has six.
- After a step that changes something, say what the reader should now see, when you know it.
- No background. Link to the concept page.
- One task per section. If a task has two ways (dashboard and API), give them as tabs, in the same order on every page.

### Quickstart

- One path, no choices. Pick the options for the reader and link out for the rest. Pick by description, not by a name that changes: "the newest Ubuntu image", "the smallest specification".
- End with a check that it works, then how to remove what was created so billing stops.
- Count the time honestly. If it takes five minutes, do not write two.

### Tutorial

- Say what will be built and what the reader must already have.
- Every command can be copied and run as written.
- Include a step that proves it worked, and a step that removes everything.

### Application image

- Same sections in the same order on every image page: what is on the image, connect, first-run steps, managing it, security.
- Do not repeat the application's own documentation. Link to it.

### Reference

- Tables and lists, not stories. A reader looks things up here; nobody reads it top to bottom.
- Every row fills the same columns. For a field or setting, that means name, type, unit, default and allowed values.
- This is where values that change are allowed to live. Each hand-written list needs someone who updates it.
- Same layout on every reference page. Generate it from the source where we can.

### Troubleshooting

- The title is the symptom as the customer sees it: "The web console does not open".
- Most likely cause first.
- Each check has three parts: the command, what you should see, what to do if you do not.
- End with what to send to support.

### FAQ

- Only real questions customers ask.
- The first word answers it where possible: "No. Only outbound traffic counts."
- An FAQ never owns a fact. It gives the short answer and links to the page that does.

## Glossary

| Use | Not | Why |
|---|---|---|
| compute | instance, compute instance, VM, server | The product says **New Compute**. A page may explain once that a compute is a virtual machine. |
| destroy | delete, terminate, remove | The button says **Destroy Compute**. API pages keep the API's own word. |
| turn off, turn on | power off, stop | The buttons say **Turn Off** and **Turn On**. The state that follows is called Stopped. "Shut down" is correct only for the operating system inside the compute. |
| upsize | resize, scale up, upgrade | The button says **Upsize Compute**. |
| specification | plan, size, tier | The product's word for one combination of CPU, RAM and storage. |
| shared CPU, dedicated CPU | class, plan | The two CPU types a specification can have. |
| image, application image | OS, template, distro | |
| domain | DNS zone, zone | The tab says **Domains**. |
| team; Owner, Admin, Member | organisation, project | Role names take a capital letter. |
| public IP | external IP, floating IP | |
| dashboard | console, control panel, web app | The site where you manage your team and its resources. |
| web console | terminal, browser console | The terminal in your browser. The button that opens it says **Console**. |
| credits | bonus, free money | Amounts we add to a team: promotions, refunds, referral rewards, rewards for a long-term commitment, and compensation after an incident. Credits are spent first. |
| funds | deposit, wallet, account balance | The money you topped up. Funds are spent when the credits run out. |
| **Balance**, **Credits** (the label) | balance as a word for the two added together | The number at the top of the dashboard. It shows your credits, labelled **Credits**, while you have any. After that it shows your funds, labelled **Balance**. The two are never added together. |
| top up | deposit, recharge | Topping up adds funds. |
| SC Agent | console agent | `sc-console-agent` only when naming the package or service in a command. |

## Which page owns which fact

Rule 1 only works if everyone knows where a fact lives. Add a row when a new fact gets a home.

| Fact | Page that owns it |
|---|---|
| What is billed in each compute state, including turned off | SC Compute overview |
| How long a compute takes to create | SC Compute overview |
| SSH port 222, and port 22 from some networks | Connect to a compute |
| When the web console is ready, and how it logs you in | SC Agent |
| A public IP is billed separately, and when you need one | Public IPs and the private network |
| What each role can do | Teams overview |
| How to pay, and the payment methods | Paying Your Bill |
| What credits and funds are, and that credits are spent first | Paying Your Bill |
| Bandwidth allowance and charges | Bandwidth |
| API rate limit, pagination, error format | API Introduction |
| Default TTL and how fast DNS changes take effect | DNS overview |

## Before you publish

- [ ] The page is one type, and follows that type's shape.
- [ ] Read aloud, every sentence is something you would say to a customer.
- [ ] Every name matches the glossary and every button name matches the screen.
- [ ] No fact is explained twice. Other pages give one sentence and a link.
- [ ] No list of options, versions or prices that the screen already shows.
- [ ] No more than two callout boxes.
- [ ] Old addresses redirect.
- [ ] Nothing internal, nothing unfinished.
- [ ] You ran the steps yourself on a real compute.
