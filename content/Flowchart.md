```mermaid
%%{
init: {
'theme': 'default', 
'themeVariables': {
'fontSize': '150px', 
'fontFamily': 'arial'
}
}
}%%
flowchart TD
a[<h4>START</h4>] --> B(search if recipe for DesiredApp exists on:\n<a href='https://layers.openembedded.org'>https://layers.openembedded.org</a>\n.)
B --> C{{Does a bitbake recipe\nfor DesiredApp exist?}};
C -- <b>No</b> --> E{{Is source code available\nvia a public repository\ne.g. GitHub?}};
C -- <b>Yes</b> --> D[Check if the recipe already exists\nin any of the existing layers]
D --> F{{Does the recipe\nalready exist?}};
F -- <b>No</b> --> G(Download *.bb recipes and all\ndependencies & copy them\n into layer directory under\n<b>recipes-example/DiredApp/DesiredApp.bb</b>)
F -- <b>Yes</b> --> H[Add application by appending\n<b>IMAGE_INSTALL_append= DesiredApp</b> to\n<b>mistysom-image.bbapend</b> file]
G --> I[Confirm that the layer that\nthe recipe was added to has been\nadded under <b>build/conf/bblayers.conf</b>]
I --> H
H --> K[<h4>invoke $ <b>bitbake mistysom-image</b></h4>]
E -- <b>No</b> --> L{{Is source code\n for DesiredApp is available?}}
E -- <b>Yes</b> --> M[Build recipe that clones\nsource files from repository\according to <a href='https://github.com/MistySOM/wiki/blob/master/content/HowToCreateBitbakeRecipeGitHub.md'>LINK</a>]
L -- <b>No</b> --> N[Build recipe that uses a binary file\according to <a href='#'>LINK</a>]
L -- <b>Yes</b> --> O[Build recipe that uses \nprivate source code files\according to <a href='https://github.com/MistySOM/wiki/blob/develop/content/HowToCreateBitbakeRecipeLocSrc.md'>LINK</a>]
M --> K
N --> K
O --> K

