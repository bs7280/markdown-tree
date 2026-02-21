## Hierachical note system using markdown

A markdown document itself is already a hierarchy / tree with just a few fundamental components:

1) Headings
2) bulleted lists
3) ordered lists

With everything else being "content". 

### Markdown document as a tree

```markdown
# Daily notes 2026-02-20

## Todo
Work

## Links
- [google homepage](google.com)
- [chatgpt](chatgpt.com)

## Notes

### Meetings

#### Team Meeting
Updates about stuff

#### Customer call
They requested changes for XYZ
```

Can be represented like

```python
{
  'Daily notes 2026-02-20': {
    'Todo': ["Work"],
    'Links': [['[google homepage](google.com)', '[chatgpt](chatgpt.com)']],
    'Notes': {
      'Meetings': {
        'Team Meeting': ["Updates about stuff"],
        'Customer call': ["They requested changes for XYZ"]
      }
    }
  }
}
```

Note a few things:

- Headings become nested datastructures. This is the primary source of a hierarchy in our note. They are represented as: `<heading text>: subcontent`
- Bulleted lists are arrays / list
- all other content is a list of strings.
- recursive by nature!

This example is intentionally simple. Real markdown has more features, inconsistencies between flavors of markdown, not enough type info to make this data structure reversible, etc...

My actual note system in Obsidian + a lot of niche plugins uses this exact concept for my daily notes, which enabled me to create some custom tools to easily append to my daily note by a given heading.

E.g.

```python
insert_note(`$daily_note`, '## TODO', "Buy Milk")
insert_note(`$daily_note`, '## TODO', "Email Bob")
insert_note(`$daily_note`, '## Bookmarks', "[A Beginner’s Guide to Split Keyboards | justinmklam | blog](https://www.justinmklam.com/posts/2026/02/beginners-guide-split-keyboards/)")
insert_node(`$daily_note`, '## Ideas' "Add Feature to thing to do other thing")
```

would result in:

```markdown
## Todo
- Buy Milk
- Email Bob

## Bookmarks
- [A Beginner’s Guide to Split Keyboards | justinmklam | blog](https://www.justinmklam.com/posts/2026/02/beginners-guide-split-keyboards/)

## Ideas
- Add Feature to thing to do other thing
```

See my alfred workflow to do exactly this [bs7280/Alfred-Note-Capture: Capture your thoughts, ideas, todos, and browser tabs directly to your obsidian notes without context switching](https://github.com/bs7280/Alfred-Note-Capture)

### A collection of markdown as a tree

We can extend this idea further out to an entire note system, just by storing all notes in one flat directory.

for daily notes a system would be naming them `dailynotes.YYYY.MM.DD.md` for each file. 

```
dailynotes.2026.02.md
dailynotes.2026.02.20.md
dailynotes.2026.02.19.md
dailynotes.2026.01.md
dailynotes.2026.01.25.md
dailynotes.2025.md
dailynotes.2025.12.md
dailynotes.2025.12.21.md
```

Right away this lets us do some very powerful things:
- All monthly + yearly notes are easily grouped together.
- Folders are now just notes. You can write to your overall Febuary of 2026 note, or All of 2025 note, while also using it for folder like organization.
- You can easily search for content in your daily notes like:

```
ls notes/ | grep 'dailynotes.2026.02.*'
```

To get all your notes from Febuary of 2026 

or with another note tree command:

`read_tree('dailynotes.2025.*', '## Ideas') | xargs cat * | grep 'AI Agents'` to get one bulleted list of all the ideas you had about AI Agents last year.

or 

`read_tree(dailynotes.2026.02.*, '## Todo')` to get all your TODOs this month

### Extending this concept

Another system I use is `prj.<project type>.<project name>.md`

for example:

```
prj.company.my-startup.md
prj.company.my-startup.tasks.md
prj.company.my-startup.tasks.2026-02-20.investigate-bug.md
prj.company.my-startup.tasks.2026-02-20.investigate-bug.extra-info-related-to-bug.md
prj.company.my-startup.tasks.2026-02-20.investigate-bug.claude-code-logs.md
prj.company.my-startup.tasks.2026-02-18.add-some-feature.md
prj.company.my-startup.tasks.2026-02-18.refactor-auth.mdmd
prj.company.my-startup.info.backend.system-architecture.md
prj.company.my-startup.info.backend.deployment-guide.md
prj.company.my-startup.info.gotchas.md
prj.company.my-startup.best-practices.database.md
prj.company.my-startup.best-practices.frontend.md
prj.company.my-startup.best-practices.backend.md
prj.life.ski-trip-2025.md
prj.side.polymarket-analysis-script.md
prj.homelab.md
prj.homelab.servers.md
prj.homelab.servers.NAS.md
prj.homelab.servers.Compute.md
prj.homelab.services.immich.md
prj.homelab.services.portainer.md
prj.homelab.services.seaweedfs.md
```

This sets up some very powerful capabilities. 
- Support a wide range of things:
  - a full blown startup
  - one off side projects
  - documenting my homelab

#### Startup example:
- Task management (`.tasks`)
- Knowledge base (`.info`)
- etc...

But within task management, you can add as many sub files as you want without polluting the main structure! This allows:
- Multi tasking
- Async work
- Giving a place for coding agents to go nuts with saving information while staying in their lane (more on this later).

#### Schemas for documents

In my daily note example, those files technically had a schema of needing the headers `## Todo`, `## Ideas`, `## Notes`, `## Bookmarks` in the form of a template file to create daily notes off of.

You can extend this much further and programatically. For example - my `prj.homelab.services.<service>.md` could be required to define: 
- IP address
- Login info
- Server / host
- Repo link etc...

#### Frontmatter

Small detail but, not all schemas / info should be in the main body of a markdown document. Markdown supports frontmatter which is basically just YAML in the top of your document.

```markdown
----
last_edited: <timestamp>
created: <timestamp>
server_info:
  ip_address: 192...
  ram: 12gb
  etc...
---

Extra manual notes about my server!!
```

#### Schema for files

This was originally conceptualized by [Dendron | Schemas](https://wiki.dendron.so/notes/c5e5adde-5459-409b-b34d-a0d75cbb1052/) (what I used before obsidian) which is also what introduced me to a hierarchial markdown note system.

Lets say you have a collection of docs on command line tools. You might expect it to look like:

```
info.cli.<toolname>.<commands>.<exaples>
```

with file constraints:
- tool name must be a child of info.cli
- a file per command under toolnames
- optionally - examples under the command

and file content constraints:
- toolname must include frontmatter with links to the homepage
- command must have some given note structure
  - layout with details like arguments, options, flags, etc...

Giving something like:

```
info.cli.git.add.md
info.cli.git.commit.md
info.cli.git.push.md
```

And `info.cli.git.add.md` looking like:

```
## Name

## Synposis

## Description

## Flags

### <flag 1>
```

Which now makes it much easier to document or get info now that the schema is defined all the way down to the heading. This schema also lets you reference notes that do not exist yet (like if you wanted to add an example snippet to the `git rebase` command.

### Build this base tool



### Features not covered

Things not covered.
- Visualizing this system
- Bulleted List
- Backlinks
- Infinite recursion of notes. You and I can both insert an entire shared notesystem if we are working on a project together.

## Ai Agents

How might this system work with AI Agents? I want a way to solve a few issues

1) Keep code base documented without polluting it with markdown files
2) make it easy for AI Agents to find important info from docs without using all their context
3) Have a way to save all my chat history / code changes / etc... in a clean way while still respecting 1 and 2.

This note system allows that with a well designed MCP. 

### Example

Lets say I spawn a claude session or Agent to implement some feature off my todo list. 

Current state:
```
prj.mystartup.todos.123-refactor-front-end.md
prj.mystartup.tasks.md
```

The `123-refactor-front-end.md` already has all the info written by me of what needs to be done for this. There may some formal structure like acceptance criteria, things to watch out for, instructions to not touch such and such files, etc...

but we can set the context with `markdown_mcp.start_task('123-refactor-front-end')` which then creates a new file:

`prj.mystartup.tasks.123-refactor-front-end.md`

That may look like:

```
---
created_datetime: ...
modifed_datetime: ...
task-id: ...
---

Implement: [This task](prj.mystartup.tasks.123-refactor-front-end.md)

... Task details...

## Changes

## Testing guide
```

#### Mid agent process

We can then expose hooks tools like:
- Write Note (append to `<task_file>.agent_notes.md`)
- Record git diff of final changes (`<task_file>.agent_changes.md`)
- Search project knowledge (`search('prj.mystartup.best-practices')`
- Add / Complete TODOs (append to / modify `<task_file> -> ## TODO secion`
- Add knowledge: Append to `prj.mystartup.best-practices.database`
- etc....

But most importantly - give agents free roam to write info to a specific restricted part of your notes. While also giving them a structured way of retreiving information from your note system. 

## Conclusion

Other ai agent related things:
- This makes it easier to multi task with managing MANY agents.
- Note system becomes a knowledge graph, which is more broadly searchable by an agent than regular markdown notes
- Archive all chat history + changes + commit message together in sub file.
- Tree system gives models a place to be chatty while another place to be more to the point (and link to the more detailed docs).

I only had so much time to write all this today. Theres a lot of ideas and features not mentioned yet, but the point is that by treating markdown with the right abstraction, you can do some crazy things. 
