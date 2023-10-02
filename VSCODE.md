# VSCode

VSCode makes development significantly easier for multiple reasons. 
- Easy to connect to another server with SSH for developing/running code
- Easy to connect to WSL2 on Windows for developing/running code
- Easy to debug many languages with integrated debuggers, e.g. gdb/lldb (C/C++), pdb (Python)
- Many useful extensions such as C/C++, CMake, LaTeX, etc. 
- Open source with a large community on StackOverflow and GitHub if you get stuck

Other IDEs may or may not include the same set of features, so it is up to you which IDE you want to use, if any. 

To install and start using VSCode, see [Getting started with Visual Studio Code](https://code.visualstudio.com/docs/introvideos/basics).

A few additional nodes:
- Install extensions using the side bar (`Ctrl + Shift + X`), with a few notable ones
  - [SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)
  - [C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)
  - [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack)
  - [Vim key bindings](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim)
  - [Emacs key bindings](https://marketplace.visualstudio.com/items?itemName=tuttieee.emacs-mcx)
- See [Debugging](https://code.visualstudio.com/docs/editor/debugging) for using `launch.json` to debug programs 
  - We will provide a `launch.json` file with homeworks or projects when applicable
- See [Tasks](https://code.visualstudio.com/docs/editor/tasks) for using `tasks.json` to run additional tasks, such as compiling
  - We will provide a `tasks.json` file with homeworks or projects when applicable
- See [Workspaces](https://code.visualstudio.com/docs/editor/workspaces) for how to manage multiple folders in VSCode
  - You can use these workspaces to include these tutorials as a second folder added to each hw/project, to make it easy to reference tutorials while working on a project
  - You don't need to generate from scratch, just run `Ctrl + Shift + P` -> start typing "Add folder to workspace" and select "Workspaces: Add Folder to Workspace..."

If you have additional recommendations for this tutorial please post in Slack or open a [Discussion](https://github.com/eec289q-f23/tutorials/discussions).
