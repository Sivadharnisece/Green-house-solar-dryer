# Green-house-solar-dryer
#include <stdio.h>
#include <stdbool.h>

#define SOLAR_CONSTANT 1361.0  // Solar constant in W/m^2
#define STEFAN_BOLTZMANN 5.67e-8  // Stefan-Boltzmann constant in W/m^2/K^4
#define SPECIFIC_HEAT_AIR 1005.0  // Specific heat of air in J/kg/K
#define MASS_AIR 10000.0  // Mass of air in kg
#define HEAT_CAPACITY_AIR (SPECIFIC_HEAT_AIR * MASS_AIR)  // Heat capacity of air
#define SURFACE_AREA 10.0  // Surface area of greenhouse in m^2
#define ALBEDO 0.3  // Albedo (reflectivity) of greenhouse covering
#define EMISSIVITY 0.9  // Emissivity of greenhouse covering
#define TIME_STEP 3600  // Time step in seconds
#define NUM_STEPS (24 * 7)  // Number of time steps (1 week)
#define INITIAL_TEMP 20.0  // Initial temperature in Celsius
#define HUMIDITY_THRESHOLD 60.0  // Humidity threshold in percentage

// Function to calculate greenhouse temperature
void calculate_temperature(double solar_radiation[], double ambient_temp[], double temperatures[], bool moisture_sensor[]) {
    temperatures[0] = INITIAL_TEMP;
    for (int i = 1; i < NUM_STEPS; i++) {
        double delta_Q = (1 - ALBEDO) * solar_radiation[i] - \
                         EMISSIVITY * STEFAN_BOLTZMANN * (temperatures[i-1] + 273.15)*(temperatures[i-1] + 273.15)*(temperatures[i-1] + 273.15)*(temperatures[i-1] + 273.15);
        double delta_T = delta_Q / HEAT_CAPACITY_AIR;
        temperatures[i] = temperatures[i-1] + delta_T;
        if (temperatures[i] < ambient_temp[i]) {
            temperatures[i] = ambient_temp[i];
        }

        // Simulate moisture sensor (assuming random variation)
        if (temperatures[i] > HUMIDITY_THRESHOLD) {
            moisture_sensor[i] = true;  // Humidity above threshold
        } else {
            moisture_sensor[i] = false;  // Humidity below threshold
        }
    }
}

int main() {
    // Arrays to store solar radiation, ambient temperature, greenhouse temperature, and moisture sensor readings
    double solar_radiation[NUM_STEPS];
    double ambient_temp[NUM_STEPS];
    double greenhouse_temp[NUM_STEPS];
    bool moisture_sensor[NUM_STEPS];

    // Populate solar radiation and ambient temperature arrays (random values for simulation)
    for (int i = 0; i < NUM_STEPS; i++) {
        solar_radiation[i] = SOLAR_CONSTANT;
        ambient_temp[i] = 20.0; // Assuming constant ambient temperature
    }

    // Calculate greenhouse temperature and moisture sensor readings
    calculate_temperature(solar_radiation, ambient_temp, greenhouse_temp, moisture_sensor);

    // Print results
    printf("Time (hours)\tSolar Radiation (W/m^2)\tAmbient Temperature (°C)\tGreenhouse Temperature (°C)\tMoisture Sensor\n");
    for (int i = 0; i < NUM_STEPS; i++) {
        printf("%d\t\t%.2f\t\t\t%.2f\t\t\t%.2f\t\t\t%s\n", i, solar_radiation[i], ambient_temp[i], greenhouse_temp[i], moisture_sensor[i] ? "Above threshold" : "Below threshold");
    }

    return 0;
}
