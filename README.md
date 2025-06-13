# R-Environment-Notes
Notes on using renv to manage virtual environments in R.

This seems much more straightforward than python so it's all below in the README.

---

### Check if renv is installed (if you've never installed it it won't be)

`renv` is not part of the base R installation. It is a contributed package available from CRAN (the Comprehensive R Archive Network), just like `dplyr` or `ggplot2`.
Check whether renv is installed by trying to load it with the `library()` function.

```R
library(renv)
```

**What the results mean:**

*   **If it's INSTALLED:** The command will run silently without any output, or it might print a startup message from the package. You won't get an error.

*   **If it's NOT INSTALLED:** You will get a clear error message.
    ```
    Error in library(renv) : there is no package called ‘renv’
    ```

### If its not installed install it

You only need to do this once on your computer. You install it into your main, global R library like any other package.

Open your R or RStudio console and run:

```R
install.packages("renv")
```

Once this command is finished, the `renv` package is available for you to use in any of your R projects.

#### Step 1: Initialize `renv` in a Project

You only need to do this once when you start a new project or decide to use `renv` with an existing one.

*   Navigate to your project's root directory in the R console.
*   Run the `init()` command.

```R
# Run this once per project
renv::init()
```

This command will:
1.  Create a project-specific library inside a new `renv/` folder.
2.  Create or modify the project's `.Rprofile` file to ensure `renv` is loaded automatically whenever you open the project.
3.  Discover the packages you are currently using in your scripts, install them into the new library, and create an initial `renv.lock` file by taking a snapshot.

#### Step 2: Install or Remove Packages

As you work on your project, you'll install new packages. You can do this in the standard way. `renv` is smart enough to intercept this.

```R
# Install a new package
install.packages("dplyr")

# Or, install multiple packages
install.packages(c("ggplot2", "stringr"))

# To remove a package
remove.packages("some_old_package")
```
These packages will be installed into your project's private `renv` library, not your global R library.

#### Step 3: Save Your Changes (Take a Snapshot)

After you have installed, updated, or removed packages and are happy with the state of your project, you must **save** these changes to the lockfile. This is the crucial step for reproducibility.

```R
# Save the current state of your project library to renv.lock
renv::snapshot()
```

When you run this, `renv` will:
*   Scan your R scripts for required packages.
*   Compare those packages with the state of your project library.
*   Show you which packages will be added, removed, or changed in `renv.lock`.
*   Ask for your confirmation before writing to the lockfile.

> **Best Practice:** Think of `renv::snapshot()` like `git commit`. You do it whenever you've reached a stable, meaningful state in your project's dependencies. You should add `renv.lock` to your Git repository.

#### Step 4: Restore the Environment on a New Machine

This is the "payoff" step. Imagine a collaborator (or your future self on a new computer) has just cloned your project from Git. The repository contains your R scripts and the `renv.lock` file.

To get the exact same package environment, they just need to:

1.  Open the project in R or RStudio.
2.  Run the `restore()` command. (If `renv` isn't activated it it will prompt you to activate `renv` first).

```R
# Read renv.lock and install packages to match it
renv::restore()
```
`renv` will read `renv.lock`, see which packages are needed (and their exact versions), and install them. Your collaborator now has a perfect replica of your package environment, avoiding "it works on my machine" problems.

### Other Useful Commands

*   **`renv::status()`**: Shows you the difference between the packages defined in your `renv.lock` and the packages currently installed in your library. It's great for checking if you need to run `snapshot()` or `restore()`.
*   **`renv::history()`**: Shows the history of changes to the lockfile.
*   **`renv::revert()`**: Reverts changes to the lockfile, similar to `git revert`.

