#HelloWorld

## Summary

In this lesson, we will install the development environment, configure the IDE and write a simple Hello World program under move, aptos and sui respectively

## Development environment configuration

At present, move development can only be done under linux or mac. Friends who use windows can enable WSL and use it in linux environment. If necessary, you need to use a proxy to accelerate the download speed of the module or use an acceleration node to replace it with a domestic download source. See [Appendix]( #appendix). Friends who have already installed can skip the relevant installation instructions by themselves

- [Install rust](https://rustup.rs/)
```shell
# install rustup
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# Press enter when encountering Proceed with installation (default)
# After the installation is complete, the prompt Rust is installed now. Great!
# Will prompt you to configure environment variables, follow his prompts
# E.g:
# To configure your current shell, run:
# source "$HOME/.cargo/env"
# Check if the installation was successful
cargo --version
```

- [Install move cli](https://github.com/move-language/move/blob/main/language/documentation/tutorial/README.md#step-0-installation)
```shell
# Create a project directory under the user directory
mkdir -p ~/projects && cd ~/projects
# clone project
git clone https://github.com/move-language/move.git
# Install compilation dependencies
cd move
./scripts/dev_setup.sh -ypt
# Install required dependencies
# Proceed with installing necessary dependencies? (y/N) > y
# Update the terminal environment
source ~/.profile
# Compile and install move cli (takes a while)
cargo install --path language/tools/move-cli
# Check if the installation was successful
move --version
```
- [Install aptos cli](https://aptos.dev/cli-tools/aptos-cli-tool/install-aptos-cli)
```shell
# Create a project directory under the user directory
mkdir -p ~/projects && cd ~/projects
# clone project
git clone https://github.com/aptos-labs/aptos-core.git
# Install compilation dependencies
cd aptos-core
./scripts/dev_setup.sh
# Update the terminal environment
source ~/.cargo/env
# Switch to the devnet branch
git checkout --track origin/devnet
# Compile and install aptos cli (takes a while)
cargo install --path crates/aptos
# Check if the installation was successful
aptos --version
```
- [Install sui cli](https://github.com/MystenLabs/sui/blob/main/doc/src/build/install.md)
```shell
# Create a project directory under the user directory
mkdir -p ~/projects && cd ~/projects
# clone project
git clone https://github.com/MystenLabs/sui.git
# Install compilation dependencies
cd sui
# Switch to the devnet branch
git checkout --track origin/devnet
# Compile and install sui cli (takes a while)
cargo install --path crates/sui
# Check if the installation was successful
sui --version
```
## Hello World

### hello move

```shell
# create project
mkdir -p ~/projects/move_tutorial && cd ~/projects/move_tutorial
move new hello_move
cd hello_move
```

Add & edit project files

`Move.toml` adds MoveNursery dependency
```toml
[package]
name = "hello_move"
version = "0.0.0"
[addresses]
std = "0x1"
[dependencies]
MoveStdlib = { git = "https://github.com/move-language/move.git", subdir = "language/move-stdlib", rev = "main" }
MoveNursery = { git = "https://github.com/move-language/move.git", subdir = "language/move-stdlib/nursery", rev = "main" }
```

`sources/my_module.move` creates the my_module module under 0xCAFE, including the return string of the speak method
```move
module 0xCAFE::my_module {
    use std::string;
    use std::debug;
    public fun speak(): string::String {
        string::utf8(b"Hello World")
    }
    #[test]
    public fun test_speak() {
        let res = speak();
        debug::print(&res);
        let except = string::utf8(b"Hello World");
        assert!(res == except, 0);
    }
}
```
`scripts/my_script.move` calls the my_module::speak method, prints the string
```move
script {
    use std::debug;
    use 0xCAFE::my_module;
    fun my_script() {
        debug::print(&my_module::speak());
    }
}
```

```shell
# Publish the module in the sandbox environment
move sandbox publish
# run the script
move sandbox run scripts/my_script.move
# The output string of the above command is displayed in the form of char code, we use node conversion to view the content
node -e "console.log([72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100].map(code => String.fromCharCode(code)).join('') )"
```

### hello aptos

```shell
# create project
mkdir -p ~/projects/move_tutorial && cd ~/projects/move_tutorial
aptos move init --package-dir hello_aptos --name hello_aptos
cd hello_aptos
```

`sources/my_module.move` creates the my_module module under 0xCAFE, including the return string of the speak method
```move
module 0xCAFE::my_module {
    use std::string;
    use std::debug;
    public fun speak(): string::String {
        string::utf8(b"Hello World")
    }
    #[test]
    public fun test_speak() {
        let res = speak();
        debug::print(&res);
        let except = string::utf8(b"Hello World");
        assert!(res == except, 0);
    }
}
```

```shell
# run tests
aptos move test
# The output string of the above command is displayed in the form of char code, we use node conversion to view the content
node -e "console.log([72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100].map(code => String.fromCharCode(code)).join('') )"
```
### hello sui

```shell
# create project
mkdir -p ~/projects/move_tutorial && cd ~/projects/move_tutorial
sui move new --install-dir hello_sui hello_sui
cd hello_sui
```

`sources/my_module.move` creates the my_module module under hello_sui, including the return string of the speak method
```move
module hello_sui::my_module {
    use std::string;
    public fun speak(): string::String {
        string::utf8(b"Hello World")
    }
    #[test]
    public fun test_speak() {
        assert!(*string::bytes(&speak()) == b"Hello World", 0);
    }
}
```

```shell
# Modify Move.toml
# Change rev to devnet
```

```shell
# run tests
sui move test
```
## Summarize
In this section, we learned how to build a development environment, and wrote hello world applets under move, aptos, and sui respectively. We found almost identical syntax, with something unique to each environment. Students can modify the code to compile and test at will. In addition, you can check the help content of `move`, `aptos`, and `sui` command lines to see what other interesting things are available under these commands. Next, we will focus on the Move syntax itself, learn the Move module and script related content, then we will see you next.

## Appendix

### [Windows WSL Install Ubuntu](https://docs.microsoft.com/en-us/windows/wsl/install)

```powershell
# Install Ubuntu-20.04
wsl --install Ubuntu-20.04
```

If it is the first time to install WSL, you need to restart the computer.

```powershell
# Enter the wsl environment
wsl -d Ubuntu-20.04
```

### Proxy configuration

If a `timeout` error occurs during the installation process, it means that your computer cannot download related resources normally, including compilation tools, git code warehouse, etc.

First we need to check that your proxy is valid

```shell
# Enter this command in the terminal. If the network cannot be connected, it will be stuck and no message will be returned. At this time, press ctrl+c to end the execution
curl google.com
# Enter this command in the terminal, replace ip and port with your proxy information, and the proxy will output when it is running normally
HTTP_PROXY=[ip]:[port] curl google.com
```

The proxy configuration consists of two parts:

- Configure the terminal environment proxy, which can solve the problem that most terminal running commands cannot connect normally
```shell
# Enter the command in the terminal, replace ip and port with your proxy information
export HTTP_PROXY=[ip]:[port]
export HTTPS_PROXY=[ip]:[port]
```

- Configure git proxy to solve the problem of connecting to github to download source code

```shell
# Enter the command in the terminal, replace ip and port with your proxy information
git config --global http.proxy [ip]:[port]
git config --global https.proxy [ip]:[port]
```

### replace github with domestic download source

If the configuration agent is still unstable and cannot run successfully, you can use the domestic download source to speed up the download process by adding the `gitclone.com/` prefix before `github.com`. This method is applicable to the `git clone` project source code,` Move.toml` depends on links. E.g

`https://github.com/davidiw/aptos-core.git`

add prefix

`https://gitclone.com/github.com/davidiw/aptos-core.git`

### `dev_setup.sh` script
```shell
# You can view the script description through the -h parameter, and you can find that -ypt has installed cmake clang pkg-config libssl-dev nodejs z3 cvc5 dotnet boogie and other commands, and updated the terminal configuration
./scripts/dev_setup.sh -h
# -b batch mode, no user interactions and minimal output
# -p update /home/user/.profile
# -t install build tools
# -y installs or updates Move prover tools: z3, cvc5, dotnet, boogie
# -d installs the solidity compiler
# -g installs Git (required by the Move CLI)
# -v verbose mode
# -i installs an individual tool by name
# -n will target the /opt/ dir rather than the /home/user dir. /opt/bin/, /opt/rustup/, and /opt/dotnet/ rather than /home/user/bin/, /home/ user/.rustup/, and /home/user/.dotnet/
```
### VSCode Move plugin installation

[https://marketplace.visualstudio.com/items?itemName=move.move-analyzer](https://marketplace.visualstudio.com/items?itemName=move.move-analyzer)

Search for move-analyzer plugin installation

```shell
# Enter the move source code directory
cd ~/projects/move
# Compile and install the move-analyzer command line
cargo install --path language/move-analyzer
```

Note that the plug-in and the command line are not the same, the plug-in will detect the command line installed in the system and use the command line to perform analysis

### JetBrains series IDE plugin installation

[https://plugins.jetbrains.com/plugin/14721-move-language](https://plugins.jetbrains.com/plugin/14721-move-language)

Search for the Move Language plug-in installation, and configure the location of the aptos command line. Since this plugin is developed by the Pontem development team of the aptos ecosystem, it is mainly developed for aptos, so some IDE build functions do not work properly in other environments, but it does not affect the basic functions such as syntax highlighting of move files, and can still be used.

## refer to

- [Move official repository](https://move-language.github.io/move/)
- [Move Book Chinese Version](https://github.com/move-language/move/tree/main/language/documentation/book/translations/move-book-zh)
- [Move book tutorial in English](https://github.com/move-language/move/blob/main/language/documentation/tutorial/README.md)
- [Move book tutorial Chinese version](https://move-dao.github.io/move-book-zh/move-tutorial.html)
