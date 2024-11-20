> I feel like GitHub should do a better job at fixing their plugin in time. People can't use their services and some of them are actually paying customers which is unacceptable.
> 
> We've spend a while looking into workarounds that can help us use CodeSpaces. Here is what seems to be working for us so far:
> 
> 1. Add your SSH key to Github (i.e. `~/.ssh/id_ed25519`)
> 2. Download and install [gh cli](https://cli.github.com/)
> 3. `gh auth login` to Authenticate
> 4. Start a CodeSpace
> 5. Run `gh codespace ssh --config` (might need to re-authenticate to elevate permission levels, but that will be automatic so just follow the seps)
> 6. The above command will give you an output similar to this (you can get the id via `gh codespace list`):
>    ```
>    Host cs.<id-of-codespace>.master
>      User vscode
>      ProxyCommand /home/<username>/.local/bin/gh cs ssh -c <id-of-codespace> --stdio -- -i /home/<username>/.ssh/codespaces.auto
>      UserKnownHostsFile=/dev/null
>      StrictHostKeyChecking no
>      LogLevel quiet
>      ControlMaster auto
>      IdentityFile /home/<username>/.ssh/codespaces.auto
>    ```
> 7. The above only works for `gh codespace ssh`. It will NOT work if you simply try to execute `ssh vscode@<id-of-codespace>.github.dev` it will give you errors.
> 8. Replace the above output with this one and add it to your `~/.ssh/config`. The important parts are the `Host`, `ProxyCommand` and `IdentityFile`. The rest should remain whatever the `gh cli` gave you (at least we didn't change anything)
>    ```
>    Host <id-of-codespace>.github.dev
>      User vscode
>      ProxyCommand /home/<username>/.local/bin/gh cs ssh -c <id-of-codespace> --stdio -- -i /home/<username>/.ssh/id_ed25519 <= OR WHEREVER YOUR ACTUAL KEY KEY IS
>      UserKnownHostsFile=/dev/null
>      StrictHostKeyChecking no
>      LogLevel quiet
>      ControlMaster auto
>      IdentityFile /home/<username>/.ssh/id_ed25519 <= OR WHEREVER YOUR ACTUAL KEY KEY IS
>    ```
>    
>    
>        
>          
>        
>    
>          
>        
>    
>        
>      
>    You can also use the following command to do the same thing (bash / sh / zsh / etc):
>    ```shell
>    IDENTITY_FILE=~/.ssh/id_ed25519; 
>    gh codespace ssh --config | sed -E "s#(ProxyCommand.*) -i((.+)codespaces.auto)#\1 -i $IDENTITY_FILE#g; s#(IdentityFile).+codespaces.auto#\1 $IDENTITY_FILE#g; s/cs\.(.+)\.(.+)-github-codespace/\1.github.dev/"
>    ```
> 9. Test the connection with `ssh -vv vscode|codespace@<id-of-codespace>.github.dev`
> 10. Start JetBrains Gateway and select `SSH` connection instead of Github Codespaces
>     ![image](https://private-user-images.githubusercontent.com/2303663/304123727-ec15a1d6-3329-4c2d-87b4-87e208d1f919.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MzIwNzQzODUsIm5iZiI6MTczMjA3NDA4NSwicGF0aCI6Ii8yMzAzNjYzLzMwNDEyMzcyNy1lYzE1YTFkNi0zMzI5LTRjMmQtODdiNC04N2UyMDhkMWY5MTkucG5nP1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI0MTEyMCUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNDExMjBUMDM0MTI1WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9MWRmOWVhZDA1NDJmOTJiYTU0MGU2M2RlNmMyM2E3N2VjYzhjZmQ0M2ZkOWQ4NGI3NTFhZDAyNjhjNzA2NTAyNiZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.5Y_RSSjE_O1iwQx__n9OjzbKmyacXGBVjczH2T-8Pak)
> 11. Everything should be working.

