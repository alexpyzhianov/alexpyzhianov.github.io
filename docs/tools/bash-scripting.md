---
sidebar_position: 1
---

# Bash Scripting

Almost no one I know takes bash scripting seriously. Show your fellow developer a `deploy.sh` and they'll run for cover screaming. But if you're ready to suspend your disbelief for a moment, bash scripting can be a powerful tool to automate and coordinate the execution of shell commands and other scripts.

## Installation

If you're on Mac, you probably have an _ancient_ version of bash installed by default. Like several major versions behind. Do yourself a favor and install the latest version with Homebrew:

```bash
brew install bash
```

Don't forget update your shell configuration file (`.bashrc`, `.zshrc`, etc.) with something like:

```bash
export PATH=/opt/homebrew/bin:$PATH
```

## When to use it

Bash is a great tool for **running other tools**. It's usually not a good idea to **build stuff** with bash, but it's a great idea to **coordinate** the execution of other programs and scripts. Use it when:

- you need to automate a simple chain of shell commands.
- you're working on a small project by yourself.
- no one else will ever (EVER) see your script.

## When not to use it

As I said, bash is not a good tool for building stuff. Don't use it when:

- you need to write a complex script (what is "complex" is up to you).
- you need exceptional reliability and proper error handling.
- you need to share your script with others at work (you still need your job, right?).
- you're shipping production code in a Fortune 500 company.

## How I use it

I use bash almost daily to automate tasks related to my local development environment. As a full-stack developer working with microservices, I often need to start and stop them, pull the latest changes from the repository, and run migrations on my development database.

All in all, it's usually dozens of commands that I need to run in a specific order. I could do it manually, but I hate boring repetitive tasks. Also it's error-prone. So I have a couple of bash scripts that do all that for me.

Here is a simplified excerpt from one of my scripts. It reads a CSV file with the names of the services I'm working on, and for each one, it pulls the latest changes from the repository, installs the dependencies, runs the migrations, and starts the service with pm2.

```bash
function for_all_repos() {
    while IFS=, read -r service_name migrations_command start_command
    do
        cd $service_name
        git pull --rebase
        npm install
        eval $migrations_command
        eval "pm2 start \"$start_command\" --name $service_name"
    fi
    done < repos.csv
}
```

This `repos.csv` file could look like this:

```csv
backend,npm run migrations,npm run start
auth,npm run migrations,npm run dev
frontend,echo "No migrations",npm run start
admin,echo "No migrations",npm run start:watch
```

## Alternatives

### Ansible

Ansible is a more powerful tool for automation. It's much more suitable for tasks like provisioning servers, deploying applications, and managing infrastructure. It also has a steeper learning curve and requires a bit more setup. For simple tasks, it provides almost no benefit over bash.

### Docker Compose

Lol wut? Docker Compose is a tool for defining and running multi-container Docker applications. But I often see people using it **purely** to automate tasks, that are often as simple as starting a two or three microservices locally. Note, that if you're working on Mac, you'll have to run a literal VM first to even run a single Docker container. Go buy that MacBook Pro with 32GB of RAM, naughty boi.

### Any other programming language

If you're writing a script that is more complex than just glueing together a few shell commands, you're probably better off using a more powerful programming language. Python, and Node.js are both great choices for writing scripts. They have better error handling, better support for complex data structures, and better libraries. But they require more setup and can tempt you to over-engineer your solution.

## Conclusion

Bash is a great tool for automating simple tasks. It's not a good tool for building complex systems. Use it when you need to run a few shell commands in a specific order. Don't use it when you need to write a complex script, or when you need to share your script with others.
