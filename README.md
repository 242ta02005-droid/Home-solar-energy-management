# Home-solar-energy-management
# Home Solar Energy Management System

import random
import time

BATTERY_CAPACITY = 5000       # Wh
battery = 2500                # Initial battery energy
MAX_BATTERY_CHARGE = 1000     # W
MAX_BATTERY_DISCHARGE = 1000  # W

def solar_generation():
    # Simulated solar power
    irradiance = random.uniform(100, 1000)
    panel_area = 10            # m²
    efficiency = 0.20

    return irradiance * panel_area * efficiency


def home_load():
    # Simulated household load
    return random.uniform(300, 2500)


def manage_energy(solar, load):
    global battery

    surplus = solar - load
    grid_import = 0
    grid_export = 0

    if surplus > 0:
        # Solar is greater than household demand
        charge_power = min(surplus, MAX_BATTERY_CHARGE)

        battery += charge_power / 60

        if battery > BATTERY_CAPACITY:
            excess = (battery - BATTERY_CAPACITY) * 60
            battery = BATTERY_CAPACITY
            grid_export = max(0, surplus - charge_power + excess)

    else:
        # Household needs more power than solar provides
        required = abs(surplus)

        available = min(required, MAX_BATTERY_DISCHARGE)

        battery -= available / 60

        if battery < 0:
            grid_import = abs(battery) * 60
            battery = 0

    return grid_import, grid_export


def display(solar, load, battery, grid_import, grid_export):

    battery_percent = (
        battery / BATTERY_CAPACITY
    ) * 100

    print("\n" + "=" * 45)
    print("       HOME SOLAR ENERGY MANAGEMENT")
    print("=" * 45)

    print(f"Solar Generation : {solar:8.2f} W")
    print(f"Home Consumption : {load:8.2f} W")
    print(f"Battery Level    : {battery_percent:8.1f} %")
    print(f"Grid Import      : {grid_import:8.2f} W")
    print(f"Grid Export      : {grid_export:8.2f} W")

    if battery_percent <= 20:
        print("Status           : LOW BATTERY")
    elif grid_import > 0:
        print("Status           : USING GRID POWER")
    elif grid_export > 0:
        print("Status           : EXPORTING SOLAR POWER")
    else:
        print("Status           : SOLAR POWER ACTIVE")


# Main monitoring loop

for i in range(20):

    solar = solar_generation()
    load = home_load()

    grid_import, grid_export = manage_energy(
        solar,
        load
    )

    display(
        solar,
        load,
        battery,
        grid_import,
        grid_export
    )

    time.sleep(2)
