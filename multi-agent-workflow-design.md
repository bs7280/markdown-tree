
## Example workflow - Research and Data Modeling project:

PLAN.md - Top level file with info on all sub projects / areas, their todo lists, and links
tasks/
findings/
docs/

## Agents

### Planning

- Does not write any code or run commands
- Encouraged to read through as much of the repo as possible
- Encouraged to not make any assumptions without asking me unless theres an agreed upon standard of doing things for the project
- Able to start sub agents
- holds sub agent work accountable

### Implementation Agent

- Works off a specific task
- Reads things via the task file
- can write detailed info on a sub note
- can update task status into task file
- able to trigger a "repo detailed" agent which only has read access

### Research and analysis agent
For data science / experiments / data analysis type work. Final output may be a simple script + details on what was learned. Designed to play into larger research efforts where a parent agent tracks whats known / unknown / new ideas.

## Human Operator interfaces

- Quickly inject TODOS and BUGS etc... into the working system. Should not require an LLM to add things! In manual multi session work streams, its easy to forget or get overwhealmed by a stream of ideas / todos from testing while agents churn out tasks.
- System for documenting the high level info. E.g. Table of contents of commands to run
  - can control-f or use a fast agent to quickly ask "whats the command to pull data from March 2025-June 2025" -> `uv run pull_data --start 2025-03-01 --end 2025-06-01`
