# Audio Switcher Setup Instructions

## Installation

1. **Install the main tool:**
   ```bash
   # Create bin directory if it doesn't exist
   mkdir -p ~/bin

   # Copy the audio-switch tool to ~/bin/audio-switch
   chmod +x ~/bin/audio-switch

   # Add ~/bin to PATH if not already there (add to ~/.bashrc or ~/.profile)
   export PATH="$HOME/bin:$PATH"
   ```

2. **Install the widget script:**
   ```bash
   # Copy the widget script
   cp audio-widget.sh ~/bin/audio-widget.sh
   chmod +x ~/bin/audio-widget.sh
   ```

3. **Dependencies:**
   ```bash
   # Required: pipewire/wireplumber (you already have these)
   # Optional but recommended:
   sudo apt install dmenu zenity libnotify-bin

   # dmenu - for lightweight menu selection
   # zenity - fallback GUI dialogs
   # libnotify-bin - for notifications
   ```

## Usage

### Command Line Interface

```bash
# Switch to predefined profiles
audio-switch profile headphones    # Scarlett headphones + fancy mic
audio-switch profile speakers      # Motherboard speakers + webcam
audio-switch profile podcast       # Speakers + fancy mic for recording

# Individual device control
audio-switch set-output scarlett
audio-switch set-input webcam
audio-switch volume-out 80%
audio-switch volume-in 60%

# Status and information
audio-switch status                # Current setup
audio-switch list                  # Available devices
audio-switch profiles              # Available profiles
audio-switch help                  # Full help
```

### Taskbar Widget Setup

#### Option 1: XFCE4 Panel Custom Launcher

1. Right-click on xfce4-panel → "Panel" → "Add New Items"
2. Add "Launcher"
3. Right-click the new launcher → "Properties"
4. Set:
   - **Name:** "Audio Profiles"
   - **Comment:** "Switch audio profiles"
   - **Command:** `/home/robert/bin/audio-widget.sh menu`
   - **Icon:** Choose audio-related icon (e.g., `audio-headphones`)

#### Option 2: MATE Panel Applet

1. Right-click on mate-panel → "Add to Panel"
2. Add "Custom Application Launcher"
3. Set:
   - **Name:** "Audio Profiles"
   - **Command:** `/home/robert/bin/audio-widget.sh menu`
   - **Icon:** Choose from audio icons

#### Option 3: Desktop Launcher

```bash
# Create desktop file
cat > ~/.local/share/applications/audio-switch.desktop << 'EOF'
[Desktop Entry]
Version=1.0
Type=Application
Name=Audio Profile Switcher
Comment=Switch between audio profiles
Icon=audio-headphones
Exec=/home/robert/bin/audio-widget.sh menu
Categories=AudioVideo;Audio;
EOF
```

## Widget Behavior

- **Click widget** → Shows menu with:
  - `headphones` - Scarlett headphones + fancy mic
  - `speakers` - Motherboard speakers + webcam
  - `podcast` - Speakers + fancy mic
  - `🔊 Output Only` - Choose output device only
  - `🎤 Input Only` - Choose input device only
  - `📊 Status` - Show current audio status

- **Uses dmenu** if available (keyboard-friendly)
- **Falls back to zenity** for GUI dialogs
- **Shows notifications** for profile changes (if libnotify available)

## Customization

### Adding New Profiles

Edit the `PROFILES` section in `audio-switch`:

```coffeescript
bluetooth:
  description: "Bluetooth headphones + bluetooth mic"
  output:      { device: "bluetooth_sink", volume: "85%" }
  input:       { device: "bluetooth_source", volume: "75%" }
```

### Adding New Devices

When you connect Bluetooth or other devices, add them to the `DEVICES` section:

```coffeescript
DEVICES =
  sinks:
    # ... existing devices ...
    bluetooth: { id: XX, name: "Bluetooth Device Name", device: "bluetooth.device.name" }
  sources:
    # ... existing devices ...
    bluetooth_mic: { id: XX, name: "Bluetooth Mic", device: "bluetooth.mic.device" }
```

To find new device IDs and names:
```bash
wpctl status
pactl list short sinks
pactl list short sources
```

### Keyboard Shortcuts

Add global keyboard shortcuts in your desktop environment:

**XFCE4:**
1. Settings → Keyboard → Application Shortcuts
2. Add shortcuts like:
   - `Ctrl+Alt+1` → `audio-switch profile headphones`
   - `Ctrl+Alt+2` → `audio-switch profile speakers`
   - `Ctrl+Alt+3` → `audio-switch profile podcast`

**MATE:**
1. System → Preferences → Hardware → Keyboard Shortcuts
2. Add custom shortcuts with the same commands

## Troubleshooting

### Device Not Found Errors

If you get "device not found" errors:

1. **Check current devices:**
   ```bash
   wpctl status
   pactl list short sinks
   pactl list short sources
   ```

2. **Update device IDs** in the `DEVICES` section if they've changed

3. **Test individual commands:**
   ```bash
   wpctl set-default 60  # Test with actual device ID
   ```

### Bluetooth Issues

- Bluetooth devices only appear when connected
- Device IDs may change between connections
- Consider creating a separate bluetooth detection script

### Widget Not Appearing

- Verify script permissions: `ls -la ~/bin/audio-widget.sh`
- Test manually: `~/bin/audio-widget.sh menu`
- Check panel launcher command path is correct

## Integration with Other Tools

### Volume Control Integration

The tool works alongside:
- `mate-volume-control` - GUI volume control
- `pavucontrol` - Advanced PulseAudio control
- `wpctl` commands - Direct pipewire control

### Hotkey Integration

Can be bound to:
- Media keys (if available)
- Custom keyboard shortcuts
- Mouse button bindings
- Gesture recognition software

### Notification Integration

Supports:
- Desktop notifications via `notify-send`
- Custom notification scripts
- Status bar integration (like i3status, polybar)