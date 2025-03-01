+++
title = "Nix-Darwin on MacOS"
author = ["Seth Martin"]
date = 2025-02-21T15:57:00-08:00
tags = ["nix-darwin", "macos", "homebrew", "system-management"]
categories = ["Development", "Efficiency"]
draft = true
+++

I'm going to show you how to setup Nix-Darwin on your Mac to manage your entire computer, its Homebrew packages and more. By the end of this tutorial, you will have your Mac as close to declaratively setup as possible. Let's get started, shall we?


## Background {#background}


### What is Nix-Darwin? {#what-is-nix-darwin}

Nix is a declarative (explicitly written) package-manager and system-manager. It can also be a standalone operating system that rivals and perhaps even exceeds Arch in its level of capablity and polish (yes, I have had a fully homebaked version of Arch from scratch and it was awesome).

Nix-Darwin is a package-management system for MacOS (think Homebrew) that uses the same syntax as Nix - the operating system. Nix is an entire world unto itself.


### Why would I want that? {#why-would-i-want-that}

Instead of depending on some behind the scenes magic to install things on your computer, you open a text-buffer in your favorite editor and have a written list of "packages". If you want to install a package, you just add the package to the list, save the file, and run a nix-reload command and viola that package is installed.

We can even fully manage our Homebrew packages inside of our nix configuration file (meaning you always know what brew has installed).

Want a package and everything to do with it removed from your computer? Open the text file, delete that package from your list, save the file, and nix-reload.

Did something break on your system because of an update and you can't spend time fixing it right now? You can run a nix-rollback command and your system reverts to the previous state without any fuss.

Need to setup a new computer? Just clone down the configuration file and run nix-refresh and your new computer is in parity with your old computer down to the user list and personal settings.

Run both mac and linux? Share the bulk of your configuration and maintain git doped parity between computers, even across different operating systems.

If the above didn't wet your pallet, I'm not sure what would. You should probably leave because we are diving right in.


## Overview {#overview}

The high-level workflow we will going for looks like this:

1.  Download nix-darwin
2.  Open the configuration file, make some updates, and save it
3.  Run a nix-reload
4.  Enjoy your declarative life

This is going to be the best thing you've done for your development environment in awhile. Let's get started


## Setup {#setup}

We are going to setup your computer somewhat similarly to mine. I'll explain the choices I make along the way and try to clarify where you can change things out to your own taste


### Installing Nix Darwin {#installing-nix-darwin}

The most robust means to install Nix Darwin at the time of writing is by the Determinate Systems installer (this is even reflected in the Nix Darwin readme). You can also manually install nix, but it makes uninstalling it a bit more of a process. The determinate system installer handles all of the setup and keeps a record of what it did so that, should you opt out of nix in the future, you have a single uninstall command to get rid of everything.

Run the following command in your shell:

```bash
curl --proto '=https' --tlsv1.2 -sSf -L https://install.determinate.systems/nix | \
  sh -s -- install --determinate
```

This command installs [Determinate Systems - Nix Installer](https://github.com/DeterminateSystems/nix-installer?tab=readme-ov-file#determinate-nix-installer)" on your Macbook. Once complete, you now have nix installed!


#### Verify Nix Installation {#verify-nix-installation}

One very cool and useful more often than you might realize capability of the nix is to run transient nix shells with different software installed without installing that software on your machine.

Reload your terminal and run this command

```bash
nix-shell -p asciiquarium --run asciiquarium
```

This will create a "temporary shell" with "asciiquarium" installed and runs it. Enjoy the fishes. Ctrl+C to terminate it -- now if you just try to run `asciiquarium` you'll get a command not found error.

If this worked for you, congratulations you have nix installed on your computer!


#### Disclaimer: {#disclaimer}

Nix-darwin creates a separate "nix" user and disk structure on your computer to maintain a pure, isolated environment for nix packages. This is why nix-darwin is somewhat more involved and why determinate systems is a good choice as it makes the uninstallation process much faster than a manual and tedious process.

It also does a better job of ensuring your nix setup is as mac friendly as possible and ready to go with some extra features. Do your own research, but from my experience using both, the determinate installer is great.


#### Disclaimer: {#disclaimer}

The files below are comment heavy because I assume someone following this may not be fully versed in Nix.


### Creating your nix structure {#creating-your-nix-structure}

We are going to create a nix directory that contains all of our nix-stuff. This is your homebase, where you will manage everything on your computer from here on out including installed applications, brew packages, dotfiles, and more.

Let's create the following directory structure in our computer:

```bash
nix
├── README.md
├── darwin
│   ├── default.nix
│   ├── packages.nix
│   ├── services.nix
│   └── system.nix
├── dotfiles
├── flake.nix
├── home.nix
└── personal.nix
```

If you are really feeling lazy, here's a shell command for you:

```bash
mkdir -p ~/nix/darwin ~/nix/dotfiles && \
touch ~/nix/README.md \
      ~/nix/darwin/default.nix \
      ~/nix/darwin/packages.nix \
      ~/nix/darwin/services.nix \
      ~/nix/darwin/system.nix \
      ~/nix/flake.nix \
      ~/nix/home.nix \
      ~/nix/personal.nix
```

For the sake of best practices, go ahead perform a `git init` here and sync the repo up to your github. This is a good practice in general, and when you pair git with nixos, you have a truly badass backed up declarative system.

Great, we are going to populate all of these files with some basic settings to get you inspired and educated on the power of nix. Let's go!


### Setting it up {#setting-it-up}

Now that we have our directory structure in place, let's fill in these files to create a functional Nix-Darwin configuration. Each file serves a specific purpose in our setup:


#### personal.nix {#personal-dot-nix}

This file contains your personal identity information and preferences that remain consistent across systems. It's a central place to store information about yourself that other configuration files will reference.

```nix
# personal.nix - User-specific personal preferences
{ pkgs, lib, ... }:

{
  # Personal identity information
  personal = {
    username = "yourname";  # Replace with your username
    fullName = "Your Name";  # Replace with your name
    email = "your.email@example.com";  # Replace with your email
  };

  # Universal preferences that would apply across systems
  preferences = {
    # Editor preferences
    editor = {
      default = "nvim";  # Your preferred default editor
      gui = "emacs";  # Your preferred GUI editor
    };
  };
}
```


#### flake.nix {#flake-dot-nix}

This is the entry point for your Nix configuration. Flakes are a relatively new feature in Nix that provide reproducible builds and a standard structure for Nix projects. The flake.nix file defines inputs (dependencies) and outputs (configurations).

```nix
{
  # A brief description of this configuration
  description = "Your Nix-Darwin Configuration";

  # INPUTS: External dependencies needed for your configuration
  # These are like package imports or library dependencies in other languages
  inputs = {
    # The core Nix packages repository - using the darwin-specific branch
    # This is where all the software packages come from
    nixpkgs.url = "github:NixOS/nixpkgs/nixpkgs-24.11-darwin";

    # nix-darwin provides macOS-specific functionality for Nix
    # It allows Nix to configure macOS settings and work with macOS features
    nix-darwin = {
      url = "github:LnL7/nix-darwin/nix-darwin-24.11";
      inputs.nixpkgs.follows = "nixpkgs"; # Use the same nixpkgs as above
    };

    # home-manager manages user-specific configuration (dotfiles, programs)
    # This is like having a separate configuration for your user account
    home-manager = {
      url = "github:nix-community/home-manager/release-24.11";
      inputs.nixpkgs.follows = "nixpkgs"; # Use the same nixpkgs as above
    };

    # nix-homebrew allows us to manage Homebrew via Nix
    # This gives us the best of both worlds - Nix's reproducibility with Homebrew's macOS software
    nix-homebrew.url = "github:zhaofengli-wip/nix-homebrew";

    # Utilities for handling macOS applications
    mac-app-util.url = "github:hraban/mac-app-util";

    # Homebrew repositories (marked as flake = false because they aren't Nix flakes)
    # These are the sources for Homebrew packages
    homebrew-core = {
      url = "github:homebrew/homebrew-core";
      flake = false; # Not a Nix flake, just a regular Git repository
    };
    homebrew-cask = {
      url = "github:homebrew/homebrew-cask";
      flake = false; # Casks are GUI applications for macOS
    };
    homebrew-bundle = {
      url = "github:homebrew/homebrew-bundle";
      flake = false; # Bundle functionality for Homebrew
    };
  };

  # OUTPUTS: What this flake provides - in our case, a macOS system configuration
  # This is where we use the inputs above to create our system
  outputs =
    # Use inputs@ to capture all inputs and then destructure specific ones we need
    inputs@{
      self, # Reference to this flake
      nix-darwin, # The darwin configuration system
      nixpkgs, # All the packages
      home-manager, # User environment manager
      nix-homebrew, # Homebrew integration
      mac-app-util, # macOS application utilities
      homebrew-core, homebrew-cask, homebrew-bundle, # Homebrew repositories
    }:
    let
      # Specify the system architecture
      system = "aarch64-darwin";  # For Apple Silicon Macs, use "x86_64-darwin" for Intel

      # Import our personal configuration file with user-specific settings
      # This passes needed Nix tools to the personal.nix file
      personalConfig = import ./personal.nix {
        inherit (nixpkgs) lib; # Nix helper functions
        pkgs = nixpkgs.legacyPackages.${system}; # All available packages
      };

      # Create a machine name using the username from personal config
      # This makes it easy to identify which machine configuration is which
      machineName = "${personalConfig.personal.username}-mac";
    in
    {
      # Define a Darwin (macOS) system configuration with our username
      # This is the main output of our flake - a complete system configuration
      darwinConfigurations.${machineName} = nix-darwin.lib.darwinSystem {
        inherit system; # Use the system architecture we defined above

        # Modules are like components or plugins that make up our configuration
        modules = [
          # Core system configuration directory
          ./darwin

          # macOS application utilities module
          mac-app-util.darwinModules.default

          # Homebrew integration module
          nix-homebrew.darwinModules.nix-homebrew

          # Inline module for configuring Homebrew
          {
            nix-homebrew = {
              enable = true; # Turn on Homebrew management
              enableRosetta = true; # Enable Rosetta for Intel apps on Apple Silicon
              user = personalConfig.personal.username; # User that will own Homebrew
              mutableTaps = false; # Make Homebrew taps immutable (managed by Nix)
              autoMigrate = true; # Auto-migrate existing Homebrew setup
              taps = {
                # Connect Homebrew taps to the Git repositories we imported
                "homebrew/homebrew-core" = homebrew-core;
                "homebrew/homebrew-bundle" = homebrew-bundle;
                "homebrew/homebrew-cask" = homebrew-cask;
              };
            };
          }

          # Inline module for user setup and home-manager integration
          {
            # Make our personal config available to all modules as 'personal'
            # This lets other modules access our user preferences
            _module.args.personal = personalConfig;

            # Configure the user's home directory
            users.users.${personalConfig.personal.username}.home = "/Users/${personalConfig.personal.username}";

            # home-manager configuration for managing user environment
            home-manager = {
              useGlobalPkgs = true; # Use the global nixpkgs instead of a separate one
              useUserPackages = true; # Install user packages to user profile

              # Configure home-manager for our user
              users.${personalConfig.personal.username} =
                { pkgs, ... }:
                import ./home.nix {
                  inherit pkgs; # Pass the packages set
                  lib = nixpkgs.lib; # Pass Nix helper functions
                  personal = personalConfig; # Pass our personal configuration
                  config = { }; # Empty config - could be used for overrides
                };

              # Make personal config available within home-manager modules
              extraSpecialArgs = {
                personal = personalConfig;
              };
            };
          }

          # Import the home-manager darwin module
          home-manager.darwinModules.home-manager
        ];
      };
    };
}
```


#### home.nix {#home-dot-nix}

This file configures your user environment using home-manager, which is like Nix for your home directory. It manages your dotfiles, user programs, and shell configuration.

<!--list-separator-->

-  Concerning Dotfiles

    It is worth emphasizing here that from now on, when you want to update a dotfile, you do so from your `~/nix/dotfiles` directory. Now is a good time to copy any dotfiles you already have into this directory and specify them as below.

    What happens with dotfiles here is that the actual dotfile location gets symlinked to your nix dotfile location. This gives you:

    -   Fully declared dotfiles managed via nix
    -   Dotfiles backed up via git and github

    Remember, changes to these locations won't take affect until you run the nix-rebuild command!

    ```nix
    {
      pkgs,
      lib,
      config,
      personal,
      ...
    }:
    let
      # Get the directory containing this file
      configDir = builtins.toString ./.;
    in
    {
      home.stateVersion = "24.11";

      # SPECIFY DOT FILES - these are symlinked to your home directory
      home.file = {
        ".config/nvim/init.lua".source = "${configDir}/dotfiles/nvim/init.lua";
        # Add more dotfiles as needed
      };

      programs = {
        home-manager.enable = true;

        # CONFIGURE GIT
        git = {
          enable = true;
          userName = personal.personal.fullName;
          userEmail = personal.personal.email;
          extraConfig = {
            init.defaultBranch = "main";
            pull.rebase = true;
            core.editor = personal.preferences.editor.default;
          };
        };

        # CONFIGURE TERMINAL
        alacritty = {
          enable = true;
          settings = {
            window = {
              decorations = "none";
              padding = {
                x = 5;
                y = 5;
              };
              opacity = .95;
            };
          };
        };

        # CONFIGURE ZSH
        zsh = {
          enable = true;
          enableCompletion = true;
          autosuggestion.enable = true;

          sessionVariables = {
            EDITOR = personal.preferences.editor.default;
            VISUAL = personal.preferences.editor.gui;
            HOMEBREW_BIN = "/opt/homebrew/bin";
            HOMEBREW_SBIN = "/opt/homebrew/sbin";

            # Assembled PATH with all components
            PATH = "$HOMEBREW_BIN:$HOMEBREW_SBIN:$PATH";
          };

          shellAliases = {
            # Helpful alias to rebuild your Nix configuration
            nxr = "darwin-rebuild switch --flake ~/nix#${personal.personal.username}-mac";
            ls = "ls --color=auto";
          };
        };
      };
    }
    ```


#### darwin/default.nix {#darwin-default-dot-nix}

This is the entry point for your Darwin-specific configuration. It imports all other Darwin modules to form your system configuration. You can rename these files however you want or re-organize them into whatever way makes sense to you.

```nix
# Main entry point for Darwin configuration - imports all other modules
{ config, pkgs, ... }:
{
  imports = [
    ./system.nix  # System settings and fonts
    ./packages.nix  # All packages and Homebrew config
    ./services.nix  # Various services
  ];
}
```


#### darwin/packages.nix {#darwin-packages-dot-nix}

This file defines all the system-wide packages to install using both Nix and Homebrew.If you have a multi-user setup or want some packages relegated just to your user, I recommend you check in with an AI assistant (Claude is pretty good) to reconfigure things.

In short, you can also include packages using `home.packages = with pkgs; [ htop tmux git ];` in your home.nix file if you prefer packages set to just your user. I'm a single user, so system wide is fine for me.

```nix
{ pkgs, ... }:
{
  # SYSTEM PACKAGES
  environment.systemPackages = with pkgs; [
    # DEVELOPMENT TOOLS
    git  # Version control system
    curl  # Data transfer tool
    wget  # File download utility
    coreutils  # GNU file, shell and text utilities
    neovim  # Text editor

    # USER TOOLS
    alacritty  # Terminal emulator
    # Add more packages as needed
  ];

  # HOMEBREW CONFIGURATION AND PACKAGES
  homebrew = {
    enable = true;
    onActivation = {
      cleanup = "zap";  # Remove anything not declared
      autoUpdate = true;  # Update formulae on activation
      upgrade = true;  # Upgrade packages on activation
    };

    taps = [
      "homebrew/homebrew-core"  # Main package repository
      "homebrew/homebrew-cask"  # GUI applications
      "homebrew/homebrew-bundle"  # Brewfile support
    ];

    brews = [
      "ripgrep"  # Fast text search tool
      "fd"  # Simple, fast file finder
      "node"  # JavaScript runtime
      # Add more brew packages as needed
    ];

    casks = [
      "firefox"  # Web browser
      "visual-studio-code"  # Code editor
      # Add more applications as needed
    ];
  };
}
```

<!--list-separator-->

-  Managing Homebrew

    One of the most powerful aspects of this setup is the ability to manage your Homebrew packages declaratively through Nix. But you might be wondering - if we're using Nix, why bother with Homebrew at all?

    <!--list-separator-->

    -  Why Use Both Nix and Homebrew?

        While Nix is excellent for reproducible configurations, there are several practical reasons to maintain Homebrew integration:

        1.  ****Mac-Specific Applications****: Many macOS applications are only available through Homebrew Casks and aren't packaged in Nixpkgs, or are better maintained in Homebrew.

        2.  ****GUI Application Support****: Homebrew Casks handle macOS GUI applications more seamlessly, including proper app registration, icon integration, and Spotlight indexing.

        3.  ****Mac App Store Integration****: The \`mas\` CLI tool (managed via Homebrew) lets you install Mac App Store applications programmatically.

        4.  ****Familiar Developer Experience****: If you're collaborating with other Mac developers, Homebrew provides a familiar interface they may already understand.

        5.  ****Commercial Software****: Some commercial software with licensing requirements may work better through Homebrew's installation methods.

        6.  ****Weird Edge Cases****: Some developer tooling gets really hair with nix. I'm not sure why, but if I run into issues installing something via nix, I often nuke it and just install via brew through Nix.

        Our setup gives us the best of both worlds - Nix's declarative configuration and rollback capabilities with Homebrew's extensive macOS support.

    <!--list-separator-->

    -  Mac App Store Integration with MAS

        Let's update our \`packages.nix\` file to include Mac App Store applications using the \`mas\` CLI:

        ```nix
        { pkgs, ... }:
        {
          # SYSTEM PACKAGES
          environment.systemPackages = with pkgs; [
            # Previous packages...
          ];

          # HOMEBREW CONFIGURATION AND PACKAGES
          homebrew = {
            enable = true;
            onActivation = {
              cleanup = "zap";
              autoUpdate = true;
              upgrade = true;
            };

            taps = [
              "homebrew/homebrew-core"
              "homebrew/homebrew-cask"
              "homebrew/homebrew-bundle"
            ];

            brews = [
              "ripgrep"
              "fd"
              "node"
              "mas"  # Mac App Store CLI
            ];

            casks = [
              "firefox"
              "visual-studio-code"
            ];

            # Mac App Store applications
            # The format is: "Application Name" = App Store ID
            # You can find the ID by searching for the app in the Mac App Store,
            # clicking on it, and looking at the number in the URL or running: `mas search <<appname>>` in the terminal
            masApps = {
              "Xcode" = 497799835;
              "1Password" = 1333542190;
              "Things" = 904280696;
              # Add more Mac App Store apps as needed
            };
          };
        }
        ```

        With this configuration, applications from the Mac App Store will be automatically installed and managed as part of your Nix setup.

    <!--list-separator-->

    -  Potential Issues with Nix-Only Approach

        While Nix is powerful, there are some challenges when using it as your only package manager on macOS:

        1.  ****Integration with macOS****: Some Nix-installed applications may not integrate as well with macOS features like Spotlight, Services menu, or file associations.

        2.  ****Application Signing****: macOS security features like Gatekeeper may interact differently with Nix-installed applications compared to Homebrew or Mac App Store installations.

        3.  ****Learning Curve****: The Nix language and ecosystem have a steeper learning curve compared to Homebrew's simple CLI commands.

        4.  ****Binary Cache Availability****: Some macOS-specific packages may not be available in binary caches, requiring local compilation which can be time-consuming.

        Our approach with nix-homebrew integration solves many of these issues while maintaining the benefits of declarative configuration.

    <!--list-separator-->

    -  Migrating from an Existing Homebrew Setup

        If you're already using Homebrew with a collection of packages, migrating to a Nix-managed Homebrew setup is straightforward. Here's how to transition smoothly:

        1.  ****Inventory Your Current Homebrew Packages****

            First, let's capture what you currently have installed:
            ```bash

            # Get a list of all formulae (CLI packages)
            brew leaves > my_brew_packages.txt

            # Get a list of all casks (GUI applications)
            brew list --cask > my_brew_casks.txt

            # Get a list of all taps (third-party repositories)
            brew tap > my_brew_taps.txt

            # List Mac App Store applications (if mas is installed)
            if command -v mas &> /dev/null; then
              mas list > my_mas_apps.txt
            fi
            ```

        2.  ****Transfer to Your Nix Configuration****

            Now add these packages to your \`packages.nix\` file:
            ```nix
            homebrew = {
              enable = true;
              onActivation = {
                cleanup = "zap"; # This will remove anything not declared
                autoUpdate = true;
                upgrade = true;
              };

              # Add your existing taps
              taps = [
                "homebrew/homebrew-core"
                "homebrew/homebrew-cask"
                "homebrew/homebrew-bundle"
                # Add taps from my_brew_taps.txt
              ];

              # Add your existing brew packages
              brews = [
                # Add packages from my_brew_packages.txt
                "ripgrep"
                "fd"
                # etc.
              ];

              # Add your existing casks
              casks = [
                # Add casks from my_brew_casks.txt
                "firefox"
                "visual-studio-code"
                # etc.
              ];

              # Add your Mac App Store apps
              masApps = {
                # Format: "App Name" = App ID
                # From my_mas_apps.txt
              };
            };
            ```

        3.  ****Converting Mac App Store IDs****

            The output of \`mas list\` shows IDs and names, but you'll need to reformat them for your Nix configuration:
            ```bash
            # Example mas list output:
            # 497799835 Xcode (14.3)
            # 1333542190 1Password 7 (7.9.5)

            # Needs to be reformatted as:
            masApps = {
              "Xcode" = 497799835;
              "1Password 7" = 1333542190;
            };
            ```

        ****Integration Strategy****

        You have two options when migrating:

        -   ****Full Migration****: Add all packages to your Nix configuration immediately
        -   ****Gradual Migration****: Start with a minimal set and add packages as needed

        The gradual approach is often more manageable, as it lets you verify each package works correctly with your Nix setup.

        ****Dealing with Potential Conflicts****

        When you first run \`darwin-rebuild switch\`, nix-homebrew tries to take control of your Homebrew installation. If you encounter issues:

        ```bash
        # Backup your existing Homebrew installation
        cp -r /opt/homebrew /opt/homebrew-backup

        # Let nix-darwin rebuild run
        darwin-rebuild switch --flake .#username-mac

        # If needed, restore specific parts from backup
        ```

        ****Verify Your Migration****

        After rebuilding your system with the new configuration:

        ```bash
        # Check that your packages are installed
        brew list

        # Verify Mac App Store apps
        mas list
        ```

        With this approach, you'll have successfully migrated your existing Homebrew setup to be managed by Nix, getting the best of both worlds: your familiar tools plus Nix's reproducibility and declarative management.

    <!--list-separator-->

    -  Caveats and Edge Cases

        -   ****Custom Builds****: If you have packages installed with custom options using \`brew install --with-xyz\`, you'll need to specify those options in your nix-homebrew configuration.

        -   ****Pinned Versions****: Homebrew under Nix will typically use the latest versions. If you need specific versions, consider using Nix packages instead, which support precise versioning.

        -   ****Post-Install Scripts****: If you ran custom scripts after installing certain packages, you'll need to incorporate those into your Nix configuration.


#### darwin/system.nix {#darwin-system-dot-nix}

This file configures macOS-specific system settings, preferences, and fonts. You can control pretty much everything on your mac from here. Some examples shown below:

```nix
# System-level settings and font configuration
{
  pkgs,
  config,
  lib,
  personal,
  ...
}:

let
  # Favorite apps for the dock
  favoriteApps = [
    "/Applications/Firefox.app"
    "/Applications/Visual Studio Code.app"
    "/Applications/Alacritty.app"
  ];
in
{
  # SYSTEM CONFIGURATION AND STATE
  system.stateVersion = 4;  # System state version

  # SECURITY AND AUTHENTICATION
  security.pam.enableSudoTouchIdAuth = true;  # Enable TouchID for sudo

  # MAC SYSTEM SETTINGS
  system = {
    defaults = {
      dock = {
        autohide = true;
        orientation = "bottom";
        show-recents = false;
        tilesize = 48;
        persistent-apps = favoriteApps;
      };

      finder = {
        FXPreferredViewStyle = "clmv";  # Use column view as default
        ShowPathbar = true;
        ShowStatusBar = true;
      };

      NSGlobalDomain = {
        AppleShowAllFiles = true;  # Show hidden files
        AppleShowAllExtensions = true;  # Show file extensions
        KeyRepeat = 2;  # Faster key repeat rate
        InitialKeyRepeat = 15;  # Reduce delay to keyrepeat start
        AppleICUForce24HourTime = true;  # Use 24 hour time
      };
    };
  };

  # NIXPKGS CONFIGURATION
  nixpkgs = {
    config.allowUnfree = true;  # Allow installation of non-free packages
    # Note: hostPlatform is already set in flake.nix via the system variable
  };

  # FONT CONFIGURATION
  fonts.packages = with pkgs; [
    (nerdfonts.override {
      fonts = [
        "JetBrainsMono"
        "FiraCode"
      ];
    })
  ];
}
```


#### darwin/services.nix {#darwin-services-dot-nix}

This file configures macOS services and launch agents.

```nix
# Services configuration
{ config, pkgs, ... }:
{
  services = {
    # You can enable and configure various services here
    # For example, yabai (tiling window manager), skhd (hotkey daemon), etc.
  };

  # You can also configure launchd agents here
  launchd.user.agents = {
    # Example: Start an application at login
    example-app = {
      serviceConfig = {
        ProgramArguments = [
          "/Applications/YourApp.app/Contents/MacOS/YourApp"
        ];
        KeepAlive = true;
        RunAtLoad = true;
      };
    };
  };
}
```

After populating these files, you'll need to build your configuration. Navigate to your nix directory and run:

```bash
cd ~/nix
darwin-rebuild switch --flake .#username-mac
```

**Please note** If this is your first time running this, you might encounter an error that says darwin-rebuild isn't installed. If that's the case, simply run:

`nix run nix-darwin/nix-darwin-24.11#darwin-rebuild -- switch --flake ~/nix#username-mac`

Replace "username-mac" with the machine name you specified in your flake.nix file. This command will evaluate your configuration, build it, and switch your system to use it.

After the installation is complete, you can run the convenience alias `nxr` to execute the full rebuild going forward.


## Daily Use {#daily-use}

Anytime you want to make a change, update the corresponding section in your nix environment, make a commit, and then run `nxr` from your terminal.


## Continued Learning {#continued-learning}

I actually have Nix setup on both my Mac and my Thinkpad, what is beautiful is that I manage both computers using essentially the same nix files. This means that all of my settings, programs, and systems are in perfect parity at all times.

If I update my terminal font, or install a new app (like Obsidian), I can update one file, save it, nix-reload to confirm, and push that to git.

I switch to my thinkpad, I git pull and nix-reload and viola, I suddenly have the new program also configured as it was on my mac.

If that is something you'd love to see, reply to [this discussion](https://github.com/sgoodluck/sgoodluck.github.io/discussions/2) on Github. It involves some refactoring of the above.


## Summary {#summary}

There you have it, a thoroughly managed system via Nix.
