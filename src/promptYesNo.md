# Prompting for a Yes/No response

Short bash function routine to prompt the user to enter `Yes` or `No` (which may be abbreviated to `Y` or `N`) in response to a prompt.

The function displays the prompt and will continue to prompt the user until they enter a valid response or press `ctrl-C` to abort the script. (The user can't just press enter to continue, though it would be trivial to change the function to allow them to do so).

```sh
confirm() {
#
# syntax: confirm [<prompt>]
#
# Prompts the user to enter Yes or No and returns 0/1.

  local _prompt _default _response
 
  if [ "$1" ]; then _prompt="$1"; else _prompt="Are you sure"; fi
  _prompt="$_prompt [y/n] ?"
 
  # Loop forever until the user enters a valid response (Y/N or Yes/No).
  while true; do
    read -r -p "$_prompt " _response
    case "$_response" in
      [Yy][Ee][Ss]|[Yy]) # Yes or Y (case-insensitive).
        return 0
        ;;
      [Nn][Oo]|[Nn])  # No or N.
        return 1
        ;;
      *) # Anything else (including a blank) is invalid.
        ;;
    esac
  done
}
```

# Example

I have a command that I want to run only if it is confirmed. Lets say I want to check the available disk space when I confirm:

```sh
confirm() {
    ...
}

echo "Check for available disk space " && confirm "Please confirm"
if [ $? -eq 0 ]; then
    df -h
fi
```

