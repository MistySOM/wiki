```mermaid
flowchart TD
a[START] --> B[search if DesiredAdd exists on\n<a href='https://layers.openembedded.org'>https://layers.openembedded.org</a>]
B --> C{Does bitbake\nrecipe exist?};
C -- No --> E{Is source code available\nvia a public repository\ne.g. GitHub?};
C -- Yes --> D[Check if the recipe already exists\nin any of the existing layers]
D --> F{Does the recipe\nalready exist?};
F -- No --> G[Download *.bb recipes and all\ndependencies & copy them\n into layer directory under\n<b>recipes-example/DiredApp/DesiredApp.bb</b>]
F -- Yes --> H[Add application by appending\n<b>APPLICATION_INSTALL_append= DesiredApp</b> to\n<b>mistysom-image.bbapend</b> file]
G --> I[Confirm that the layer that\nthe recipe was added to has been\nadded under build/conf/bblayers.conf]
I --> H
H --> K[invoke $ bitbake mistysom-image]
E -- No --> L{Source code\n for DesiredApp is available?}
E -- Yes --> M[Build recipe that clones\nsource files from repository\according to <a href='#'>LINK</a>]
L -- No --> N[Build recipe that uses a binary file\according to <a href='#'>LINK</a>]
L -- Yes --> O[Build recipe that uses \nprivate source code files\according to <a href='#'>LINK</a>]
M --> K
N --> K
O --> K

