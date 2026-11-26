# Handover manual: the method without the tool

This is the plugin's handover logic written for a person. It says what a complete maintenance handover
contains, how to tell what is missing, and which questions to ask about it. Nothing here needs the
plugin, Obsidian or a model: a spreadsheet and the project's documents are enough.

The Maintenance Handover plugin applies the same rules mechanically, and its handover report is
scored by them. How to run a handover with the plugin is in its README.

## The scope it serves

Every question and priority below is judged against this situation:

- The product is software built (mostly) by us and running (mostly) on **the client's infrastructure**.
  Access, backups and installs may be the client's job, so many questions ask "us or the client?".
- Contacts are the people and desks **we** contact: the client's side first, our own colleagues second.
- Some clients give maintainers no access to live systems. "No access, the client's operations team
  acts for us" is a complete answer, not a gap.
- Long-term plans are mostly written during maintenance. At handover, only changes already planned are
  asked for.

A project that differs from this (for example, one we host ourselves) uses the same method with more
items marked not applicable.

## The method

1. **Collect** whatever exists, in whatever form: wiki pages, emails, runbooks, contracts, notes from a
   conversation. Do not ask anyone to fill in a form first.
2. **Inventory** what the material talks about: each service, database, environment, tool, person and
   procedure, once, with what the material says about it ("What to inventory").
3. **Check coverage**: go through the 38 topics and mark each covered or not, by its rule ("The
   coverage checklist").
4. **Check each item**: for every database, environment, tool and so on in the inventory, check that it
   has the details its kind needs ("Details per item").
5. **Ask** the question that belongs to each gap and each missing detail. Write the answer down as a
   new dated note and add it to the material.
6. **Repeat** from step 2 until what remains open is either answered or marked not applicable with a
   reason.

The point of the order is that the person handing over is never asked "is your documentation
complete?". They contribute what they have, and the method tells them what is missing.

## Ground rules

These are what make the result trustworthy. Each exists because its absence produced a wrong handover
that looked right.

1. **Every fact has a source.** Record the document it came from and the sentence that states it,
   copied, not paraphrased. A fact nobody can point to in a document is not in the inventory, however
   plausible it is.
2. **Completeness is counted, not judged.** A topic is covered when its rule is met, not when someone
   feels it is well enough described. This keeps two reviewers, or two reviews a month apart, to the
   same answer.
3. **Know which item a fact is about.** "Moved it to the new VM, IP is 10.0.5.20" does not say what
   moved. If the document doesn't name the item, ask its author before recording the fact; a first name
   alone ("Priya") is a question too when the inventory has a "Priya Raman".
4. **One name per item.** "Okta", "Okta SSO" and "Acme Okta" are one entry. Keep a glossary of the
   project's own names and abbreviations and use it to merge them.
5. **Environments keep the client's names.** Record what kind each one is separately (development,
   test, pre-production, production), because the client's "Staging" may be either of two kinds, and an
   environment called "Integration" is still an environment. Where something is hosted ("the client's
   infrastructure", "the Acme cluster", a datacenter) is not an environment: record it as the
   environment's hosting.
6. **Secrets are pointers.** Record where a credential is kept (a password-manager path), never its
   value. The credential list and the access checklist are filled in by a person, never assembled from
   documents.
7. **Two documents that disagree are a conflict, not a choice.** See "When documents disagree".
8. **Not applicable needs a reason**, written down, per item. See "Not applicable".

## What to inventory

An inventory item is a durable thing. An address, a host name, a command or a version number is never
an item of its own; it is something recorded *about* an item.

| Kind | What it is | Write down |
|---|---|---|
| Component | A service or part of the product we maintain | what it is responsible for; languages, frameworks and runtimes with versions; application roles and what each unlocks |
| Datastore | A database or other store, one entry per instance | what it holds; its technology and version; which procedure backs it up |
| Schema | A schema inside a datastore, or where schemas are defined | where it is defined; how migrations are made and applied |
| Environment | A named deployment, under the client's name | its kind; purpose and hosting; address; how a maintainer connects, or "none" and who acts for us; what is missing or stubbed there; where its logs are |
| Integration | A system the product exchanges data with at runtime | what is exchanged and in which direction; whose it is; who to contact when it fails |
| Tool | Software a maintainer uses, ours or third-party | its kind; what it is for; where it is; how to get access |
| Credential | A pointer to a secret | the password-manager path only |
| Procedure | An action someone carries out | its kind; who performs it (us or the client); how often, or what triggers it; the steps |
| Contact | A person or team | role; relationship to the project; how to reach them |
| Failure mode | A problem that keeps happening | the symptom and the usual fix |
| SLA | A response or resolution commitment | priority levels and their times; support hours; where it is defined |
| Communication channel | A chat channel, mailing list or meeting | what goes there |
| Planned change | A dated commitment | when it is due; who drives it; what it changes |
| Opportunity | An improvement idea or pain point, with no date | what would improve, and what it would take |

One item may be both a Tool and an Integration (a message broker the product talks to, whose management
screen a maintainer opens). Any other item that seems to be two kinds at once is usually two items, or
one that was misread.

One process is one Procedure, however many steps it has. What the product does by itself is not a
procedure unless someone runs, schedules or looks after it; neither is a team's area of responsibility.

A priority level with its response times ("P1 Critical, 30 minutes") is an SLA, not a failure mode,
however grave its definition sounds. There is no "technical debt" kind: what the next maintainer must
know about it is a caveat on the item concerned, and what removing it would take is an Opportunity.

### Fixed kinds

Coverage reads these values, never an item's name, so a topic is covered whether the client says
"Prod", "Live" or "PRD". Use exactly one of the listed values (several when several fit).

| For | Values |
|---|---|
| Procedure | installation, deployment, login, operation, access-request, access-prerequisite, recreation, account-creation, developer-setup, ticket-lifecycle, backup-restore |
| Tool | monitoring, source-control, remote-access, documentation, product, service-desk, other |
| Contact (relationship) | customer, service-desk, previous-developer, internal, third-party |
| Environment (kind) | development, test, pre-production, production |

*Access-prerequisite* is what must be completed before any access is granted (training, a signed
agreement, a managed laptop); *access-request* is how access is then asked for. *Operation* is routine
maintenance work: log checks, restarts, reruns, renewals. *Product* tools are the admin CLIs, management
screens and scripts that ship with the product.

A document often names a well-known product without saying what it is (a table row reading "Grafana"
and an address). Keep a short list in the glossary of what such names are (`monitoring: Grafana,
Kibana`), and treat a listed name as a Tool of that kind. The list only classifies names the documents
mention; it adds nothing to the inventory by itself.

### Relations

Record how items relate, each with its source sentence like any other fact.

| Relation | Read as |
|---|---|
| part of | Admin console is part of the billing service |
| deployed to | the service runs on Production |
| depends on | the service needs its database to work |
| feeds | the service sends data to the ledger (direction matters) |
| authenticates via | the service logs users in through the identity provider |
| fronted by | Production sits behind the CDN |
| monitored by | Production is watched by the dashboard tool |
| owned by | Production belongs to the client's infrastructure lead |
| approved by | promoting a release needs the delivery manager's approval |
| requires procedure | the database needs the nightly backup procedure |
| has SLA / covers | the service is covered by the response-time matrix |
| raises | a failed batch produces a ticket |
| escalates to | on-call escalates to the delivery manager |
| affects | the framework upgrade changes the service (only from a planned change) |

A table of who may access which system is not a set of relations: record it as that system's access.

## The coverage checklist

38 topics in 8 sections. A topic is **covered** when the inventory meets its rule and a **gap** when it
does not; a gap asks its question. Priority orders the gaps (high first) and does not weight the
coverage figure.

**Coverage** = covered topics ÷ (38 − topics marked not applicable).

### 1. Domain overview

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Common use cases | high | a procedure of kind *operation* exists | What routine maintenance does the system need? Say how often each task is done and what the steps are. Examples: checking logs, restarting a service, rerunning a batch job, renewing a certificate. |
| Login process | high | a component authenticates via something, or a *login* procedure exists | How do users log in to the application? Say which system checks their identity (for example Okta or Active Directory), and which roles or accounts they need. |
| Installation & deployment | high | an *installation* or *deployment* procedure exists | How is the application installed and updated on the client's infrastructure? Describe the steps, and say who does it: us or the client's operations team. |
| Architecture & tech stack | high | a component has its technology recorded | Which programming languages, frameworks and versions does each part of the system use? Say where the versions are set. |
| Component responsibilities | high | at least one component is in the inventory | What are the main parts of the system, and what does each one do? |
| Integrations | high | at least one integration | Which other systems does the application exchange data with? Say which of them belong to the client, and who is responsible for each one. |
| Databases | high | at least one datastore | Which databases or other data stores does the project use, and what does each one store? |
| Schemas | high | at least one schema | Where are the database schemas defined? Say how schema changes (migrations) are created and applied. |
| Backup & restore | high | a *backup-restore* procedure exists | How are the databases backed up, and how is a backup restored? Say which databases are backed up, how often, where the backups are kept, who is responsible for them (us or the client), and how a restore is tested. |
| Project terminology | medium | the glossary has at least one entry | Which names, abbreviations or code names would a new maintainer not understand? Examples: environment names, job names, the client's internal product names. |

*Purpose* (what the product is for) is written when the material states it, and is not scored.

### 2. Permissions and access

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Access prerequisites | high | an *access-prerequisite* procedure exists | What must a maintainer do before the client gives them access? Say how long it takes. Examples: security training, a signed NDA, a personal account, a laptop from the client. |
| Access requirements | high | any item has its access recorded, or anything authenticates via something | Which accounts and permissions does a maintainer need? Examples: the client's network or VPN, remote desktop, roles in the application, code repositories. |
| Access request procedures | high | an *access-request* procedure exists | How does a maintainer request each kind of access, and who approves it? Say which requests go through the client. |

*Credential references* and the *access checklist* are filled in by a person and are not scored.

### 3. Environments

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Environments & hosting | high | an environment has its purpose recorded | Which environments exist, and what is each one used for? Say where each one is hosted: in the client's data center, in the cloud, or with us. |
| Environment addresses | high | an environment has an address | What is the address (URL or host name) of each environment? |
| Connection method | high | an environment has its access recorded | How does a maintainer connect to each environment? Say what they need first. Examples: VPN, jump host, web portal. |
| Bug reproduction | medium | an environment of kind *development* or *test* exists | Where can developers reproduce a bug from production? Say whether there is an environment like production, with all the external systems connected, that developers can use for this. |
| Dependency limits | medium | an environment has its limitations recorded | Which external systems are missing or simulated in each environment? Say what can't be tested there because of that. |
| Environment recreation | high | a *recreation* procedure exists | If an environment were lost, how would you rebuild it? Say who would do it (us or the client), and how much work it is. |

### 4. Development and operation

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Developer setup | high | a *developer-setup* procedure exists | What does a developer need to install and set up to build and run the project on their own computer? |
| Account creation | high | an *account-creation* procedure exists | How is a new user account created in the application? Say who can create one, for example an administrator or a contact at the client. |
| Roles & permissions | high | a component has its roles recorded | Which user roles does the application have, and what can each role do? |
| Non-intuitive behaviour | medium | any item has a caveat recorded | Does anything in the application work in an unexpected way? Examples: hidden requirements, features that need extra setup, things that look broken but are not. |

### 5. Tools

List every tool with its kind and purpose, then check:

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Monitoring & logging | high | a *monitoring* tool exists | Which tools do you use for logs, monitoring and alerts? Say whether we can use them, or only the client. Examples: Elastic, Grafana. |
| Source control & CI/CD | high | a *source-control* tool exists | Where is the source code kept, and what builds and releases it? Say who controls the access. Examples: Azure DevOps, GitHub, a build pipeline. |
| Remote access | high | a *remote-access* tool exists, or an environment is reached through one (a VPN, jump host, remote desktop, Citrix) | Which tools do you use to connect to the client's systems? Examples: Citrix, remote desktop (RDP), a jump host. |
| Requirements & documentation | high | a *documentation* tool exists | Where does the client keep the requirements and business rules? Say which source is right when two of them disagree. Examples: a wiki, Word files, emails. |
| Product tooling | medium | a *product* tool exists | Which administration tools or scripts come with the product, and what is each one for? Examples: command-line tools, administration pages. |

### 6. Contacts

List every contact by relationship, with role and how to reach them, then check:

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Customer contacts | high | a *customer* contact exists | Who are the contacts on the client's side, and what is each one responsible for? Examples: product owner, testers, key users. |
| Service desks | high | a *service-desk* contact exists | Which service desks are there, and what does each one help with? Say how to contact each one. Examples: access requests, infrastructure problems. |
| Previous developers | medium | a *previous-developer* contact exists | Who worked on the product before, and what does each person know best? |
| Internal contacts | low | an *internal* contact exists | Who in our own company can help with this project, and with what? |
| Communication channels | high | at least one communication channel | Which channels does the project use to communicate, and what is each one for? Examples: chat, mailing lists, regular meetings. |

### 7. SLA coverage

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Ticket lifecycle | high | a *ticket-lifecycle* procedure exists | Where do tickets arrive, and what happens to a ticket until it is closed? Say who sorts and assigns the tickets. If the client has its own ticket system, say how tickets move between their system and ours. |
| Response times | high | at least one SLA | How quickly must we respond to a ticket and solve it, for each priority? Say what the support hours are, and where this is written (the contract, the ticket system). |
| Escalation path | high | something escalates to someone | Who do we contact when a ticket is critical or close to its deadline? Say in which order, on our side and on the client's side. |
| Common cases | medium | at least one failure mode | Which problems happen most often, and how is each one usually solved? |

### 8. Long-term plans

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Planned changes | medium | at least one planned change | Are any changes planned? Say when they are due and who is responsible. Examples: framework upgrades, migrations, the client's own plans. |

*Opportunities and pain points* are listed when the material has them, and are not scored.

## Details per item

A topic asks whether the project has something at all. A detail asks whether each item of a kind has
what it needs: one database with a backup procedure covers "Backup & restore", and says nothing about
the second database. Go through the inventory item by item. Details are listed separately from the
gaps and are not part of the coverage figure.

| For each | It needs | Otherwise ask |
|---|---|---|
| Datastore | a procedure it requires (its backup) | How is {name} backed up and restored, and who does it? |
| Integration | an owner, someone it escalates to, or a support contact | Who do we contact if {name} stops working: a person, a team or a support page? |
| Procedure of kind *operation* | a frequency or a trigger | How often is {name} done? If there is no schedule, what starts it? |
| Item with access recorded (other than "none") | an approver, or the procedure for requesting it | How do you request access to {name}, and who approves it? |
| Environment | an address | What is the address of {name}? |
| Environment | how to connect; if "none", preferably who acts for us | How does a maintainer connect to {name}? If that is not possible, who does the work there instead? |
| Environment | a purpose | What is {name} used for, and where is it hosted? |
| Environment | a monitoring tool, or where its logs are | Where can we find the logs of {name}, or who can send them to us? |
| Procedure | who performs it | Who does this task: {name}? Is it us or the client? |
| Tool | where it is and how to get access | Where can we find {name}, and how does a maintainer get access to it? |
| Contact | role, relationship and how to reach them | What is the role of {name}, and how do we contact them? |
| Contact | if it has contact details, ones stated within the last two years | The contact details of {name} ({reach}) were written down more than two years ago. Are they still correct? |
| Environment | if it has an address, one stated within the last two years | The address of {name} ({address}) was written down more than two years ago. Is it still correct? |
| SLA | where it is defined | Where is the {name} SLA written: in the contract, or in the ticket system? |
| Planned change with a date | a date that has not passed | {name} was planned for {when}. Did it happen? If it was moved, what is the new date? |

The two-year rows ask about a value stated only once. A newer document that repeats it, a different
value (a conflict) or a person's decision closes them.

## When documents disagree

Facts fall into three groups, and only the first can conflict:

- **One true value**: an address, how to connect, an owner, a link, a due date, how to reach someone, a
  frequency, a trigger, where something is defined. Two documents giving different values is a
  **conflict**, and it blocks: the handover is not finished while one is open.
- **Several values can all be true**: technologies, and the fixed kinds. Keep them all.
- **Wording**: purpose, description, limitations, caveats, roles, where logs are. Different wording is worth a look and
  is not an error.

For a conflict:

1. Until someone decides, the value from the **newest document** is treated as current, and the
   conflict is shown beside it. Only the current value counts toward coverage.
2. A person decides which value holds and records the decision with the date, their name and the
   reason. The older value may win: a newer note can simply be wrong.
3. The losing value stays on record as the audit trail.
4. A decision settles only the values it saw. If a third value turns up, or the losing document is
   updated after the decision, the conflict is open again.
5. If neither value is right, write a new dated note with the right one, add it to the material, and
   decide in its favour.

## Not applicable

A topic or a detail that does not apply to this project is marked not applicable with a written reason:

> Remote access — the client gives us no remote access; all changes go through their operations team.

- A not-applicable topic leaves the coverage figure's denominator and the list of gaps.
- A mark without a reason does not count.
- **Evidence wins over a waiver.** If the material does document the topic, it is covered, whatever
  the mark says.
- A planned change that happened or was dropped is closed the same way, with the reason.

## Reading the result

A finished pass produces five lists, and each means something different:

| List | Means | Done when |
|---|---|---|
| Coverage per section | how many of the scored topics the material documents | — |
| Open gaps | topics with no evidence at all, high priority first | answered, or not applicable with a reason |
| Open details | items missing something their kind needs | answered, or not applicable with a reason |
| Conflicts | one-value facts that two documents state differently | a person has decided each |
| Waiting questions | facts whose item the document doesn't name | the author has said which item, or that it is about none |

Before the result is trusted or shared, a person reads it for what counting cannot see: a secret value
that slipped into a quoted sentence, a fact attached to the wrong item, and whether the two
person-owned parts (credential references, access checklist) are filled in. Corrections go into the
material as a new or edited note, never into the result, and the pass is run again.

High coverage means the topics are documented. It does not mean the documents are right, or that a
procedure written down has ever been tried.
