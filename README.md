# Master README ;-)

This public GitHub project contains the README files I created whilst going through Udemy React training.

## GitHub Initialization Commands

Before running the commands, login to GitHub and create a private repo on GitHub. For the repo specify a `.gitignore` file using Node template. Create a README markdown file as well.

Copy the repo URL into the 2nd command below. Then run them in a terminal window in the new project.

```sh
git init
git remote add origin https://github.com/greg1951/nextjs-onward-foodies-repo.git
git pull origin main
git add .
git commit -m "first commit"
git push -u origin main
git branch develop
git checkout develop
```

## Editor Settings
The settings below can be added to the settings.json file in VS Code to use 2 spaces rather than a full tab formatting.

```properties
// The number of spaces a tab is equal to. This setting is overridden
// based on the file contents when `editor.detectIndentation` is true.
"editor.tabSize": 4,

// Insert spaces when pressing Tab. This setting is overriden
// based on the file contents when `editor.detectIndentation` is true.
"editor.insertSpaces": true,

// When opening a file, `editor.tabSize` and `editor.insertSpaces`
// will be detected based on the file contents. Set to false to keep
// the values you've explicitly set, above.
"editor.detectIndentation": false
```
# Git Commands Related to Origin

- Show remote origin repo
```sh
git remote -v
```

- How to delete a  repo origin 
```sh
git remote remove origin
```
- How to add 
```sh
git remote add origin https://github.com/greg1951/nextjs-onward-foodies-repo.git
```