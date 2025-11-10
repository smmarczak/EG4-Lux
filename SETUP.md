# EG4 6000xp Integration Setup Guide

## Your Inverter Configuration

**EG4 6000xp Details:**
- IP Address: `192.168.5.103`
- Port: `8000`
- Serial: `3454660112`
- Datalog: `BA32402393`

## Quick Start

### 1. Prerequisites

- Home Assistant with MQTT broker installed
- Rust toolchain (for building from source)
- Docker (optional, easier deployment)

### 2. Configuration

Your `config.yaml` is already configured with your 6000xp details. You need to update:

```yaml
mqtt:
  host: localhost        # Change to your MQTT broker IP
  username:              # Add if using authentication
  password:              # Add if using authentication
```

### 3. Deployment Options

#### Option A: Docker (Recommended)

```bash
cd /home/user/EG4-Lux/lxp-bridge-master

# Build the container
docker build -t lxp-bridge .

# Run the container
docker run -d \
  --name lxp-bridge \
  --network host \
  -v /home/user/EG4-Lux/config.yaml:/app/config.yaml:ro \
  --restart unless-stopped \
  lxp-bridge
```

#### Option B: Docker Compose

Create `docker-compose.yaml`:
```yaml
version: '3.8'
services:
  lxp-bridge:
    build: ./lxp-bridge-master
    container_name: lxp-bridge
    network_mode: host
    volumes:
      - ./config.yaml:/app/config.yaml:ro
    restart: unless-stopped
```

Run: `docker-compose up -d`

#### Option C: Build from Source

```bash
cd /home/user/EG4-Lux/lxp-bridge-master

# Install Rust (if not installed)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Build
cargo build --release

# Run
./target/release/lxp-bridge --config /home/user/EG4-Lux/config.yaml
```

#### Option D: Home Assistant Add-on

1. In Home Assistant, go to: **Settings** → **Add-ons** → **Add-on Store**
2. Click the 3 dots (top right) → **Repositories**
3. Add: `https://github.com/celsworth/lxp-bridge`
4. Install "LXP Bridge" from the add-on store
5. Configure with your inverter details
6. Start the add-on

## What You'll Get

### Home Assistant Entities (60+)

**Core Monitoring:**
- Battery: SOC, voltage, current, temperature, health
- Solar: PV1/PV2 voltage and power (6000xp has 2 strings)
- Grid: Import/export power, voltage, frequency
- Energy: Daily and lifetime counters for all flows

**Controls:**
- AC Charge on/off
- Charge Priority mode
- Forced Discharge mode
- Charge/discharge rate limits
- SOC limits
- Time-based schedules (3 slots each for AC charge, charge priority, forced discharge)

**Detailed Sensors:**
- 9 range gates (g0-g8) for distance sensing (if applicable)
- Internal/radiator temperatures
- Fault and warning codes
- BMS cell voltages/temperatures
- EPS (backup) power monitoring

### MQTT Topics Structure

Base: `lxp/BA32402393/` (your datalog serial)

**Real-time Data:**
- `lxp/BA32402393/inputs/all` - Combined sensor data (JSON)
- `lxp/BA32402393/inputs/1` - Input registers 0-39
- `lxp/BA32402393/inputs/2` - Input registers 40-79
- `lxp/BA32402393/inputs/3` - Input registers 80-119

**Configuration:**
- `lxp/BA32402393/hold/21` - Function register (AC charge, etc.)
- `lxp/BA32402393/hold/64-161` - Various settings

**Commands:**
- `lxp/cmd/BA32402393/set/ac_charge/1` - Enable AC charge
- `lxp/cmd/BA32402393/set/charge_priority/1` - Enable charge priority
- `lxp/cmd/BA32402393/set/hold/64/2500` - Set charge power to 2500W

## 6000xp vs 18kPV Differences

| Feature | 6000xp | 18kPV |
|---------|--------|-------|
| Power | 6kW (9kW surge) | 18kW continuous |
| PV Strings | 2 | 3 |
| Phase | Split-phase (L1/L2) | 3-phase (R/S/T) |
| Generator Input | ❌ No | ✅ Yes |
| Battery Voltage | 48V nominal | Higher voltage |

**What this means:**
- `v_pv_3` / `p_pv_3` will always be 0 (no 3rd string)
- Generator sensors won't have data
- Use `v_eps_l1` / `v_eps_l2` for split-phase backup power
- `v_ac_s` / `v_ac_t` may float (not meaningful on single-phase)

## Troubleshooting

### Can't Connect to Inverter

1. **Check network:** `ping 192.168.5.103`
2. **Check port:** `telnet 192.168.5.103 8000`
3. **Verify inverter network settings** on LCD screen
4. **Check firewall** on inverter or host

### No Data in Home Assistant

1. **Check MQTT broker** is running
2. **Check MQTT credentials** in config.yaml
3. **Look at lxp-bridge logs:** `docker logs lxp-bridge`
4. **Enable HA MQTT discovery:** Should be auto-detected

### Wrong Serial/Datalog Numbers

Check the LCD screen on your inverter:
- **Serial**: Usually on the front label or in system info
- **Datalog**: Check WiFi/network settings page

## Optional Enhancements

### Enable InfluxDB (Time-Series Database)

```yaml
influx:
  enabled: true
  url: http://localhost:8086
  username: lxp
  password: lxp
  database: lxp
```

Benefits: Long-term data storage, advanced graphing

### Enable Time Sync

```yaml
scheduler:
  enabled: true
  timesync_cron: "0 0 * * *"  # Sync clock daily at midnight
```

### Database Logging

```yaml
databases:
- enabled: true
  url: sqlite://lxp.db  # Or postgres/mysql
```

## Next Steps

1. **Test the connection** - Verify lxp-bridge can connect to your inverter
2. **Check Home Assistant** - New entities should appear under MQTT
3. **Create dashboards** - Organize your 60+ entities
4. **Set up automations** - Time-based charging, SOC management, etc.

## Getting Help

- Original project: https://github.com/celsworth/lxp-bridge
- Active fork: https://github.com/jaredmauch/eg4-bridge
- Home Assistant community forums
- Check logs: `docker logs -f lxp-bridge`

## Useful MQTT Commands

**Read current settings:**
```bash
mosquitto_pub -t 'lxp/cmd/BA32402393/read/hold/21' -m ''
```

**Enable AC charge:**
```bash
mosquitto_pub -t 'lxp/cmd/BA32402393/set/ac_charge/1' -m ''
```

**Set charge power to 3000W:**
```bash
mosquitto_pub -t 'lxp/cmd/BA32402393/set/hold/64/3000' -m ''
```

**Set AC charge time (00:30 - 06:00):**
```bash
mosquitto_pub -t 'lxp/cmd/BA32402393/set/ac_charge_time/0' -m '00:30-06:00'
```
