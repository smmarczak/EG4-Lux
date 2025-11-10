# EG4 Inverter Sensor Organization Guide

This guide helps you organize the 60+ entities from your EG4 inverters in Home Assistant for easier management and visualization.

## Model-Specific Sensors

### EG4 18kPV (BA32402393)
**Full feature set including:**
- ✅ 3 PV strings
- ✅ Generator input monitoring
- ✅ 3-phase grid monitoring
- ✅ All battery and control features

### EG4 6000XP (BJ40170817)
**Feature set:**
- ✅ 2 PV strings
- ❌ No generator input
- ✅ Split-phase monitoring (L1/L2)
- ✅ All battery and control features

**Auto-filtered sensors:** The integration automatically hides irrelevant sensors:
- Generator sensors (v_gen, f_gen, p_gen) - only on 18kPV
- 3rd PV string sensors (v_pv_3, p_pv_3) - only on 18kPV

## Sensor Categories

### 🔋 Essential Monitoring (Core Dashboard)
**Priority sensors for your main dashboard:**

| Sensor | Description | Unit |
|--------|-------------|------|
| `soc` | Battery State of Charge | % |
| `v_bat` | Battery Voltage | V |
| `p_battery` | Net Battery Power (charge-discharge) | W |
| `p_pv` | Total PV Production | W |
| `p_grid` | Net Grid Power (import-export) | W |
| `p_charge` | Battery Charging Power | W |
| `p_discharge` | Battery Discharging Power | W |
| `t_bat` | Battery Temperature | °C |
| `soh` | Battery State of Health | % |

**Home Assistant Group Configuration:**
```yaml
# configuration.yaml
group:
  eg4_essential_18kpv:
    name: "EG4 18kPV - Essential"
    entities:
      - sensor.lxp_3454660112_soc
      - sensor.lxp_3454660112_v_bat
      - sensor.lxp_3454660112_p_battery
      - sensor.lxp_3454660112_p_pv
      - sensor.lxp_3454660112_p_grid
      - sensor.lxp_3454660112_p_charge
      - sensor.lxp_3454660112_p_discharge
      - sensor.lxp_3454660112_t_bat

  eg4_essential_6000xp:
    name: "EG4 6000XP - Essential"
    entities:
      - sensor.lxp_4053740239_soc
      - sensor.lxp_4053740239_v_bat
      - sensor.lxp_4053740239_p_battery
      - sensor.lxp_4053740239_p_pv
      - sensor.lxp_4053740239_p_grid
      - sensor.lxp_4053740239_p_charge
      - sensor.lxp_4053740239_p_discharge
      - sensor.lxp_4053740239_t_bat
```

### ☀️ Solar Production Details

| Sensor | Description | Unit | Models |
|--------|-------------|------|--------|
| `v_pv_1` | PV String 1 Voltage | V | All |
| `v_pv_2` | PV String 2 Voltage | V | All |
| `v_pv_3` | PV String 3 Voltage | V | 18kPV only |
| `p_pv_1` | PV String 1 Power | W | All |
| `p_pv_2` | PV String 2 Power | W | All |
| `p_pv_3` | PV String 3 Power | W | 18kPV only |
| `e_pv_day` | Total Daily PV Energy | kWh | All |
| `e_pv_day_1/2/3` | Daily PV per String | kWh | All |
| `e_pv_all` | Lifetime PV Energy | kWh | All |
| `e_pv_all_1/2/3` | Lifetime PV per String | kWh | All |

**Group:**
```yaml
group:
  eg4_solar_production:
    name: "Solar Production"
    entities:
      - sensor.lxp_SERIAL_p_pv_1
      - sensor.lxp_SERIAL_p_pv_2
      - sensor.lxp_SERIAL_p_pv_3  # 18kPV only
      - sensor.lxp_SERIAL_e_pv_day
```

### 🔌 Grid Monitoring

| Sensor | Description | Unit |
|--------|-------------|------|
| `v_ac_r` | Grid Voltage Phase R/L1 | V |
| `v_ac_s` | Grid Voltage Phase S (3-phase only) | V |
| `v_ac_t` | Grid Voltage Phase T (3-phase only) | V |
| `f_ac` | Grid Frequency | Hz |
| `p_to_grid` | Power Export to Grid | W |
| `p_to_user` | Power Import from Grid | W |
| `e_to_grid_day` | Daily Export Energy | kWh |
| `e_to_user_day` | Daily Import Energy | kWh |
| `e_to_grid_all` | Lifetime Export Energy | kWh |
| `e_to_user_all` | Lifetime Import Energy | kWh |

### 🏠 EPS/Backup Power

| Sensor | Description | Unit |
|--------|-------------|------|
| `v_eps_r` | EPS Voltage Phase R/L1 | V |
| `v_eps_l1` | EPS Voltage L1 (Split-phase) | V |
| `v_eps_l2` | EPS Voltage L2 (Split-phase) | V |
| `f_eps` | EPS Frequency | Hz |
| `p_eps` | EPS Power Total | W |
| `p_eps_l1` | EPS Power L1 | W |
| `p_eps_l2` | EPS Power L2 | W |
| `s_eps` | EPS Apparent Power | VA |
| `e_eps_day` | Daily EPS Energy | kWh |
| `e_eps_all` | Lifetime EPS Energy | kWh |

### ⚡ Generator (18kPV Only)

| Sensor | Description | Unit |
|--------|-------------|------|
| `v_gen` | Generator Voltage | V |
| `f_gen` | Generator Frequency | Hz |
| `p_gen` | Generator Power | W |
| `e_gen_day` | Daily Generator Energy | kWh |
| `e_gen_all` | Lifetime Generator Energy | kWh |

### 🔧 Battery Details

| Sensor | Description | Unit |
|--------|-------------|------|
| `bat_current` | Battery Current | A |
| `max_chg_curr` | Max Charge Current | A |
| `max_dischg_curr` | Max Discharge Current | A |
| `max_cell_voltage` | Highest Cell Voltage | V |
| `min_cell_voltage` | Lowest Cell Voltage | V |
| `max_cell_temp` | Highest Cell Temperature | °C |
| `min_cell_temp` | Lowest Cell Temperature | °C |
| `bat_count` | Number of Batteries | - |
| `bat_capacity` | Battery Capacity | Ah |

### 🌡️ Temperature Monitoring

| Sensor | Description | Unit |
|--------|-------------|------|
| `t_inner` | Internal Inverter Temp | °C |
| `t_rad_1` | Radiator 1 Temperature | °C |
| `t_rad_2` | Radiator 2 Temperature | °C |
| `t_bat` | Battery Temperature | °C |

### 🎛️ Controls & Settings

| Entity | Type | Description |
|--------|------|-------------|
| `ac_charge` | Switch | Enable AC Charging |
| `charge_priority` | Switch | Charge Before Load |
| `forced_discharge` | Switch | Force Battery Discharge |
| System Charge Rate | Number | 0-100% |
| System Discharge Rate | Number | 0-100% |
| AC Charge Rate | Number | 0-100% |
| AC Charge Limit % | Number | 0-100% |
| Discharge Cutoff % | Number | 0-100% |
| AC Charge Timeslot 1/2/3 | Text | HH:MM-HH:MM |
| Charge Priority Timeslot 1/2/3 | Text | HH:MM-HH:MM |
| Forced Discharge Timeslot 1/2/3 | Text | HH:MM-HH:MM |

### 🔍 Diagnostics

| Sensor | Description |
|--------|-------------|
| `status` | Inverter Status Code |
| `fault_code` | Fault Code |
| `warning_code` | Warning Code |
| `runtime` | Total Runtime (seconds) |

## Dashboard Examples

### 1. Energy Flow Card (Power Distribution)

```yaml
type: energy-distribution
entities:
  solar:
    entity: sensor.lxp_SERIAL_p_pv
    name: Solar
  battery:
    entity: sensor.lxp_SERIAL_p_battery
    name: Battery
  grid:
    entity: sensor.lxp_SERIAL_p_grid
    name: Grid
  home:
    entity: sensor.home_power  # You'll need to create this
    name: Home
```

### 2. Battery Status Card

```yaml
type: entities
title: Battery Status
entities:
  - entity: sensor.lxp_SERIAL_soc
    name: State of Charge
  - entity: sensor.lxp_SERIAL_v_bat
    name: Voltage
  - entity: sensor.lxp_SERIAL_p_battery
    name: Power
  - entity: sensor.lxp_SERIAL_t_bat
    name: Temperature
  - entity: sensor.lxp_SERIAL_bat_current
    name: Current
  - type: divider
  - entity: sensor.lxp_SERIAL_max_cell_voltage
    name: Max Cell Voltage
  - entity: sensor.lxp_SERIAL_min_cell_voltage
    name: Min Cell Voltage
  - entity: sensor.lxp_SERIAL_max_cell_temp
    name: Max Cell Temp
  - entity: sensor.lxp_SERIAL_min_cell_temp
    name: Min Cell Temp
```

### 3. Solar Production Card

```yaml
type: vertical-stack
cards:
  - type: gauge
    entity: sensor.lxp_SERIAL_p_pv
    name: Current Production
    min: 0
    max: 18000  # Adjust for your inverter
    unit: W
  - type: horizontal-stack
    cards:
      - type: statistic
        entity: sensor.lxp_SERIAL_e_pv_day
        period:
          calendar:
            period: day
        stat_type: change
        name: Today
      - type: statistic
        entity: sensor.lxp_SERIAL_e_pv_all
        name: Lifetime
  - type: entities
    entities:
      - entity: sensor.lxp_SERIAL_p_pv_1
        name: String 1
      - entity: sensor.lxp_SERIAL_p_pv_2
        name: String 2
      - entity: sensor.lxp_SERIAL_p_pv_3  # 18kPV only
        name: String 3
```

### 4. Controls Card

```yaml
type: entities
title: Inverter Controls
entities:
  - entity: switch.lxp_SERIAL_ac_charge
    name: AC Charge
  - entity: switch.lxp_SERIAL_charge_priority
    name: Charge Priority
  - entity: switch.lxp_SERIAL_forced_discharge
    name: Forced Discharge
  - type: divider
  - entity: number.lxp_SERIAL_ac_charge_rate
    name: AC Charge Rate
  - entity: number.lxp_SERIAL_ac_charge_limit
    name: AC Charge Limit
  - type: divider
  - entity: text.lxp_SERIAL_ac_charge_timeslot_1
    name: AC Charge Time 1
  - entity: text.lxp_SERIAL_ac_charge_timeslot_2
    name: AC Charge Time 2
```

### 5. Combined Dashboard (Both Inverters)

```yaml
type: vertical-stack
title: EG4 Energy System
cards:
  # 18kPV Summary
  - type: horizontal-stack
    cards:
      - type: entity
        entity: sensor.lxp_3454660112_p_pv
        name: "18kPV Solar"
        icon: mdi:solar-power
      - type: entity
        entity: sensor.lxp_3454660112_p_battery
        name: "18kPV Battery"
        icon: mdi:battery
      - type: entity
        entity: sensor.lxp_3454660112_soc
        name: "18kPV SOC"

  # 6000XP Summary
  - type: horizontal-stack
    cards:
      - type: entity
        entity: sensor.lxp_4053740239_p_pv
        name: "6000XP Solar"
        icon: mdi:solar-power
      - type: entity
        entity: sensor.lxp_4053740239_p_battery
        name: "6000XP Battery"
        icon: mdi:battery
      - type: entity
        entity: sensor.lxp_4053740239_soc
        name: "6000XP SOC"

  # Combined Production
  - type: sensor
    entity: sensor.total_solar_production  # Template sensor
    name: Total Solar Production
    graph: line
```

## Template Sensors for Combined Stats

Create these in `configuration.yaml` to combine data from both inverters:

```yaml
template:
  - sensor:
      # Total solar production from both inverters
      - name: "Total Solar Production"
        unique_id: total_solar_production
        state: >
          {{ states('sensor.lxp_3454660112_p_pv') | float(0) +
             states('sensor.lxp_4053740239_p_pv') | float(0) }}
        unit_of_measurement: "W"
        device_class: power
        state_class: measurement
        icon: mdi:solar-power

      # Total battery power
      - name: "Total Battery Power"
        unique_id: total_battery_power
        state: >
          {{ states('sensor.lxp_3454660112_p_battery') | float(0) +
             states('sensor.lxp_4053740239_p_battery') | float(0) }}
        unit_of_measurement: "W"
        device_class: power
        state_class: measurement
        icon: mdi:battery

      # Combined grid power
      - name: "Total Grid Power"
        unique_id: total_grid_power
        state: >
          {{ states('sensor.lxp_3454660112_p_grid') | float(0) +
             states('sensor.lxp_4053740239_p_grid') | float(0) }}
        unit_of_measurement: "W"
        device_class: power
        state_class: measurement
        icon: mdi:transmission-tower

      # Average SOC across both battery banks
      - name: "Average Battery SOC"
        unique_id: average_battery_soc
        state: >
          {{ ((states('sensor.lxp_3454660112_soc') | float(0) +
               states('sensor.lxp_4053740239_soc') | float(0)) / 2) | round(1) }}
        unit_of_measurement: "%"
        device_class: battery
        state_class: measurement
        icon: mdi:battery-50

      # Daily solar production total
      - name: "Total Daily Solar"
        unique_id: total_daily_solar
        state: >
          {{ states('sensor.lxp_3454660112_e_pv_day') | float(0) +
             states('sensor.lxp_4053740239_e_pv_day') | float(0) }}
        unit_of_measurement: "kWh"
        device_class: energy
        state_class: total_increasing
        icon: mdi:solar-power

      # Self-consumption calculation
      - name: "Solar Self Consumption Rate"
        unique_id: solar_self_consumption_rate
        state: >
          {% set pv = states('sensor.total_solar_production') | float(0) %}
          {% set export = states('sensor.lxp_3454660112_p_to_grid') | float(0) +
                          states('sensor.lxp_4053740239_p_to_grid') | float(0) %}
          {% if pv > 0 %}
            {{ (((pv - export) / pv) * 100) | round(1) }}
          {% else %}
            0
          {% endif %}
        unit_of_measurement: "%"
        icon: mdi:home-lightning-bolt
```

## Automation Examples

### Off-Peak Charging (Example)

```yaml
automation:
  - alias: "EG4 - Start AC Charge at Off-Peak"
    trigger:
      - platform: time
        at: "23:00:00"
    condition:
      - condition: numeric_state
        entity_id: sensor.lxp_3454660112_soc
        below: 80
    action:
      - service: switch.turn_on
        target:
          entity_id: switch.lxp_3454660112_ac_charge

  - alias: "EG4 - Stop AC Charge at Peak Start"
    trigger:
      - platform: time
        at: "07:00:00"
    action:
      - service: switch.turn_off
        target:
          entity_id: switch.lxp_3454660112_ac_charge
```

### Low Battery Alert

```yaml
automation:
  - alias: "EG4 - Low Battery Alert"
    trigger:
      - platform: numeric_state
        entity_id: sensor.lxp_3454660112_soc
        below: 20
    action:
      - service: notify.mobile_app
        data:
          title: "Low Battery Warning"
          message: "18kPV battery at {{ states('sensor.lxp_3454660112_soc') }}%"
```

## Tips for Managing Lots of Data

1. **Use Views/Tabs** - Separate dashboards for monitoring vs control
2. **Conditional Cards** - Show/hide based on state (charging, discharging, etc.)
3. **History Stats** - Track efficiency over time
4. **InfluxDB** - Enable for long-term data retention
5. **Custom Mini-Graph Cards** - Better visualization of trends
6. **Entity Filters** - Auto-group similar entities
7. **Fold-Entity-Row** - Collapse details until needed

## Recommended Add-ons

- **InfluxDB** - Time-series database for historical data
- **Grafana** - Advanced graphing and analytics
- **Energy Dashboard** - Built-in HA energy management
- **ApexCharts Card** - Advanced charting
- **Power Flow Card Plus** - Enhanced energy flow visualization
- **Auto-entities Card** - Dynamic entity grouping
