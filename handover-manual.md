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
| Common use cases | high | a procedure of kind *operation* exists | What does routine maintenance involve (log checks, restarts, batch reruns, certificate renewals), how often, and what are the steps? |
| Login process | high | a component authenticates via something, or a *login* procedure exists | How do users log in: which identity provider, and which roles or accounts do they need? |
| Installation & deployment | high | an *installation* or *deployment* procedure exists | How is the product installed or updated on the client's infrastructure, step by step, and who carries it out: us or the client's operations team? |
| Architecture & tech stack | high | a component has its technology recorded | Which languages, frameworks and runtime versions does each component use, and where is that pinned? |
| Component responsibilities | high | at least one component is in the inventory | What are the project's components, and what is each one responsible for? |
| Integrations | high | at least one integration | Which external systems does the product exchange data with, which of them are the client's, and who owns each? |
| Databases | high | at least one datastore | Which databases or other datastores does the project use, and what does each one hold? |
| Schemas | high | at least one schema | Where are the database schemas defined, and how are schema migrations created and applied? |
| Backup & restore | high | a *backup-restore* procedure exists | Which datastores are backed up, how often, where do backups live, who owns them, who runs them (us or the client), and how is a restore performed and tested? |
| Project terminology | medium | the glossary has at least one entry | Which names, abbreviations or codenames do the client and the team use that a new maintainer wouldn't know (environment names, job names, internal product names)? |

*Purpose* (what the product is for) is written when the material states it, and is not scored.

### 2. Permissions and access

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Access prerequisites | high | an *access-prerequisite* procedure exists | What must a maintainer complete before the client grants access (security training, NDA, named accounts, client-issued hardware), and how long does it take? |
| Access requirements | high | any item has its access recorded, or anything authenticates via something | Which accounts and permissions does a maintainer need: client network/VPN, remote desktop, application roles, repositories? |
| Access request procedures | high | an *access-request* procedure exists | How is each access requested, which requests go through the client, and who approves them? |

*Credential references* and the *access checklist* are filled in by a person and are not scored.

### 3. Environments

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Environments & hosting | high | an environment has its purpose recorded | Which environments exist, where is each hosted (the client's datacenter, cloud, ours), and what is each for? |
| Environment addresses | high | an environment has an address | What are the addresses (URLs or hosts) of each environment? |
| Connection method | high | an environment has its access recorded | How does a maintainer connect to each environment (VPN, bastion, portal), and what do they need first? |
| Bug reproduction | medium | an environment of kind *development* or *test* exists | Is there an environment close to production, with all third-party dependencies, reserved for developers to reproduce bugs? If not, where are bugs reproduced? |
| Dependency limits | medium | an environment has its limitations recorded | Which third-party dependencies are missing or stubbed on each environment, and what can't be tested there as a result? |
| Environment recreation | high | a *recreation* procedure exists | How is an environment recreated from nothing, who does it (us or the client), and how much effort does it take? |

### 4. Development and operation

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Developer setup | high | a *developer-setup* procedure exists | What does a developer need installed and configured to build and run the project locally? |
| Account creation | high | an *account-creation* procedure exists | How are application accounts created, and who can create them: an admin, a customer contact? |
| Roles & permissions | high | a component has its roles recorded | Which application roles exist, and which features does each one unlock? |
| Non-intuitive behaviour | medium | any item has a caveat recorded | What behaves in a non-obvious way when operating the application: hidden prerequisites, features that need extra setup, things that look broken but aren't? |

### 5. Tools

List every tool with its kind and purpose, then check:

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Monitoring & logging | high | a *monitoring* tool exists | Which tools log, monitor and alert on the product (Elastic, Grafana), and can we see them or only the client? |
| Source control & CI/CD | high | a *source-control* tool exists | Where does the code live (Azure DevOps, GitHub), which pipelines build and release it, and who controls access? |
| Remote access | high | a *remote-access* tool exists, or an environment is reached through one (a VPN, jump host, remote desktop, Citrix) | Which tools reach the client's systems (Citrix, RDP, jump host)? |
| Requirements & documentation | high | a *documentation* tool exists | Where does the customer document business logic and requirements (wiki, Word files, email), and which source wins when they disagree? |
| Product tooling | medium | a *product* tool exists | Which admin CLIs, management UIs or scripts ship with the product, and what is each one for? |

### 6. Contacts

List every contact by relationship, with role and how to reach them, then check:

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Customer contacts | high | a *customer* contact exists | Who are the client-side contacts (product owner, testers, key users), and what does each one decide or handle? |
| Service desks | high | a *service-desk* contact exists | Which service desk handles what (access requests, infrastructure incidents), and how is it reached? |
| Previous developers | medium | a *previous-developer* contact exists | Who has worked on the product before, and what do they know best? |
| Internal contacts | low | an *internal* contact exists | Who internally should a maintainer ask about this project, and about what? |
| Communication channels | high | at least one communication channel | Which channels (chat, mailing lists, meetings) are used for this project, and what goes where? |

### 7. SLA coverage

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Ticket lifecycle | high | a *ticket-lifecycle* procedure exists | Where do tickets come in (our desk, the client's system, email), and how are they triaged, assigned and closed? If the client has its own ticket system, how do tickets move between theirs and ours? |
| Response times | high | at least one SLA | What response and resolution times apply to each priority, during which support hours, and where is that defined (contract, ticketing system)? |
| Escalation path | high | something escalates to someone | When a ticket is critical or about to breach, who is it escalated to, and in what order, on our side and the client's? |
| Common cases | medium | at least one failure mode | Which issues come up most often, and how is each one usually resolved? |

### 8. Long-term plans

| Topic | Priority | Covered when | Question for a gap |
|---|---|---|---|
| Planned changes | medium | at least one planned change | Are any changes planned (framework upgrades, migrations, the client's development plans), when are they due, and who drives them? |

*Opportunities and pain points* are listed when the material has them, and are not scored.

## Details per item

A topic asks whether the project has something at all. A detail asks whether each item of a kind has
what it needs: one database with a backup procedure covers "Backup & restore", and says nothing about
the second database. Go through the inventory item by item. Details are listed separately from the
gaps and are not part of the coverage figure.

| For each | It needs | Otherwise ask |
|---|---|---|
| Datastore | a procedure it requires (its backup) | How is {name} backed up and restored, and who does it? |
| Integration | an owner, someone it escalates to, or a support contact | Who do we contact when {name} fails, a person, a team or a support URL? |
| Procedure of kind *operation* | a frequency or a trigger | How often is {name} done, or what triggers it? |
| Item with access recorded (other than "none") | an approver, or the procedure for requesting it | How is access to {name} requested, and who approves it? |
| Environment | an address | What is the address of {name}? |
| Environment | how to connect; if "none", preferably who acts for us | How does a maintainer connect to {name}, or, if they can't, who acts on it for them? |
| Environment | a purpose | What is {name} used for, and where is it hosted? |
| Environment | a monitoring tool, or where its logs are | Where are {name}'s logs, or who provides them? |
| Procedure | who performs it | Who performs {name}: us or the client? |
| Tool | where it is and how to get access | Where is {name}, and how does a maintainer get access? |
| Contact | role, relationship and how to reach them | What is {name}'s role, and how do we reach them? |
| Contact | if it has contact details, ones stated within the last two years | {name}'s contact details ({reach}) were last stated over two years ago. Are they still current? |
| Environment | if it has an address, one stated within the last two years | {name}'s address ({address}) was last stated over two years ago. Is it still current? |
| SLA | where it is defined | Where is the {name} SLA defined: the contract, or the ticketing system? |
| Planned change with a date | a date that has not passed | {name} was planned for {when}. Did it happen, and if it moved, when is it due now? |

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
