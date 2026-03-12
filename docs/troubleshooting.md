# Troubleshooting

## Module Not Loading

```bash
# Check dmesg for errors
dmesg | tail -20

# Verify firmware exists


# Reload module

sudo 

# Check module dependencies

```

## Device Not Recognized

```bash
# Check USB
lsusb | grep -i realtek

# Force module load
sudo 
dmesg | grep -i rtl

# Check interface
ip link show
```

## Network Not Working

```bash
# Verify MAC80211
modinfo cfg80211
modinfo mac80211

# Check signal
iwconfig wlan0

# Check logs
journalctl -xe | grep -i wifi
```

## Performance Issues

```bash
# Check channel
iw wlan0 info

# Monitor traffic
iftop -i wlan0

# Check interference
iw wlan0 survey dump
```
