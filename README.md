
# Project Documentation for Chainsaw

**Name:** Aparna A.  
**Official Repository:** https://github.com/WithSecureOpenSource/chainsaw

## 1. Project Overview

Chainsaw is an open-source digital forensics tool mainly used to analyse Windows event logs and look for suspicious activity.

Windows records a lot of information about what happens on a system, like login attempts, processes being executed, and changes made to the system. These records can be useful when investigating a cyberattack, but going through thousands of them manually would be pretty time-consuming.

Chainsaw makes this easier by letting us search through logs and use detection rules to find potentially suspicious events. It also supports analysing other Windows forensic artefacts, including Shimcache and Amcache.

The main problem it solves is making log analysis faster and more manageable, without having to set up an entire security monitoring platform.

It would mainly be useful for:

- **Digital forensic investigators** who need to figure out what happened on a compromised system.
- **Incident response teams** investigating security incidents.
- **SOC analysts and threat hunters** looking for suspicious activity.
- **CTF participants** working on Windows forensics challenges.

I found it interesting that a tool like this could also be useful for CTFs, since a lot of forensic challenges involve finding small pieces of evidence hidden within a large amount of data.

## 2. Repository Structure

**GitHub:** https://github.com/WithSecureOpenSource/chainsaw

The repository is organised into different folders for the source code, detection rules, tests, and other supporting files.

Some of the important ones are:

| File / Folder | Purpose |
|---|---|
| `src/` | Contains the main Rust source code of Chainsaw. |
| `src/main.rs` | Contains the main entry point of the command-line application. |
| `src/analyse/` | Contains code for different analysis features, including event log gap detection. |
| `rules/` | Contains detection rules specifically written for Chainsaw. |
| `mappings/` | Contains mappings that help Sigma rules work with Windows event log fields. |
| `tests/` | Contains tests and sample data used to check whether the tool is working correctly. |
| `.github/` | Contains GitHub configuration and automation files. |
| `README.md` | Explains what Chainsaw does, how to install it, and how to use it. |
| `Cargo.toml` | Contains project dependencies and build configuration. |
| `Cargo.lock` | Keeps track of the exact dependency versions. |
| `flake.nix` | Provides an alternative way to build the project using Nix. |

The main code is inside `src/`, while the tests are mainly in `tests/`. Some Rust modules also contain their own unit tests.

For documentation, the repository has a [README](https://github.com/WithSecureOpenSource/chainsaw) and a [GitHub Wiki](https://github.com/WithSecureOpenSource/chainsaw/wiki), which explain the available commands and features.

The project uses Cargo for building. According to the README, it can be compiled using `cargo build --release`, which creates an optimised executable.

Precompiled versions are also available from the [Releases page](https://github.com/WithSecureOpenSource/chainsaw/releases).

## 3. Technologies Used

Chainsaw is mainly written in **Rust**.

Rust makes sense for a tool like this because it is designed for performance and memory safety, which are important when processing large amounts of data.

Looking through the project's [`Cargo.toml`](https://github.com/WithSecureOpenSource/chainsaw/blob/master/Cargo.toml), some of the main libraries and tools used are:

- **Cargo:** Used to build the project, manage dependencies, and run tests.
- **evtx:** Used to read Windows event log files.
- **tau-engine:** Helps match events against detection rules.
- **clap:** Handles command-line arguments and options.
- **regex:** Supports searching for patterns in data.
- **serde, serde_json, and serde_yaml:** Used to work with structured data formats like JSON and YAML.
- **rayon:** Helps with parallel processing.

Chainsaw also supports **Sigma rules**.

Sigma is basically a way of writing detection rules for suspicious activity in logs. Rather than searching for everything manually, analysts can use these rules to identify particular patterns.

The `mappings/` folder helps connect the fields mentioned in Sigma rules to the actual Windows event log fields.

### Browser-Based Application

Chainsaw is primarily a command-line tool, and I couldn't find an official browser-based version maintained in its main repository.

However, it can export results in formats such as CSV and JSON, which could be used by other applications for further analysis or visualisation.

## 4. Contribution Workflow

Chainsaw uses GitHub to manage contributions.

The [Issues](https://github.com/WithSecureOpenSource/chainsaw/issues) section is where people can report bugs, suggest features, or discuss problems with the tool.

The [Pull Requests](https://github.com/WithSecureOpenSource/chainsaw/pulls) section is where contributors propose actual code changes for the maintainers to review.

If I wanted to contribute, I'd first check the existing issues to see if someone has already reported the same problem or suggested a similar feature.

Then I'd go through the relevant source code, make the changes on a separate branch, run the tests, and submit a pull request explaining what I changed.

If the maintainers suggest corrections or improvements, I'd make those changes before the PR gets merged.

### Pull Request I Looked At

**PR #228: Add analyse gaps subcommand for detecting selective EVTX record deletion**

**Link:** https://github.com/WithSecureOpenSource/chainsaw/pull/228

**Contributor:** Fuzzdkk  
**Status:** Merged on 27 April 2026

This PR adds a feature that helps detect suspicious gaps in Windows event logs.

Usually, when we think about log analysis, we think about searching for suspicious entries that are already there.

But what if someone deliberately deletes certain entries to hide what they did?

That's what this feature tries to help investigate.

It checks for:

- **Missing record IDs:** Gaps in the sequence of event log records.
- **Unusual time gaps:** Long periods where expected events aren't recorded.

This matters because an attacker might try to remove only specific records instead of clearing the entire log.

Of course, missing records or time gaps don't automatically mean someone tampered with the system. They just give investigators something worth looking into.

I also went through the PR discussion, where the maintainer suggested changes to the implementation and the contributor updated the code and tests.

It was interesting to see that contributing isn't necessarily just writing code and submitting it. The review process is also important, and changes can go through multiple revisions before being merged.

### What I'd Like to Improve

One thing I'd like to work on is making the gap-analysis results easier to understand.

For someone who isn't very familiar with Windows event logs, just seeing a list of gaps might not explain much.

I think it would be useful to have clearer information about which log contains the gap, why it was flagged, and what it could indicate.

To contribute something like this, I'd first go through `src/analyse/gaps.rs` to understand how the feature works. Then I'd look at how the results are currently displayed, make a small improvement, test it with sample logs, and submit a PR.

## 5. Something Interesting I Noticed

The most interesting thing I came across was the gap-detection feature.

I initially thought forensic tools mainly worked by finding suspicious things that were already recorded, like failed login attempts, unusual processes, or malicious commands.

I hadn't really considered that something missing from a log could also be useful evidence.

For example, suppose someone manages to compromise a Windows system and then tries to cover their tracks by deleting only the event logs related to their activity.

If there are unexplained gaps in the record IDs or timestamps, Chainsaw could help an investigator notice them.

They could then compare those gaps with other evidence from the system to understand whether anything suspicious happened.

One thing I'm still curious about is how the tool handles normal gaps in logging.

Just because a system hasn't recorded anything for a while doesn't necessarily mean someone deleted the logs. It could simply have been inactive.

I'd like to understand how Chainsaw decides when a time gap is unusual enough to flag, and whether this could be improved by considering how frequently that particular log normally records events.

That's probably what I liked most about the project. It made me realise that forensic analysis isn't always just about finding evidence that's present. Sometimes, figuring out why something is missing can be just as important.
