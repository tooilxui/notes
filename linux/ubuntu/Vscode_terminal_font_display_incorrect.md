## Q: font display looks awful in vscode terminal with oh-my-zsh.

## A:
   - install font `SourceCodePro + Powerline + Awesome + Regular.ttf`
    
         wget https://github.com/Falkor/dotfiles/raw/master/fonts/SourceCodePro%2BPowerline%2BAwesome%2BRegular.ttf
         double click that file and install
   - change vscode font setting (please take care about that `"'`and `'"` between font name:
   
           {
             "terminal.integrated.fontFamily": "'SourceCodePro+Powerline+Awesome Regular'",
           }
   - restart vscode and everything ok.