# Paulmann Gent 2 RGB Remote Home Assistant Blueprint

This Home Assistant blueprint provides control of lights using a Paulmann Gent 2 RGB remote (and likely compatible Sunricher ZG2868 models) via Zigbee2MQTT. 

## Description

This blueprint leverages Zigbee2MQTT to receive commands from the Paulmann Gent 2 RGB remote and translate them into actions to control your lights in Home Assistant. It aims to support most of the remote's functionalities, offering a convenient way to interact with your smart lighting.

The blueprint is designed primarily for RGB color lights but can be used with other types of lights, although some features may not be relevant or function as expected with non-RGB lighting.

**Important Considerations:**

  * **Zigbee2MQTT Requirement:** This blueprint requires you to be using Zigbee2MQTT to manage your Zigbee devices.
  * **Remote Compatibility:**  While designed for the Paulmann Gent 2 RGB remote (see [Paulmann Gent 2 RGB Remote](https://en.paulmann.com/detail/d6cceca46d334842bd3bd2008204274c)), it is believed to also be compatible with the Sunricher ZG2868 (see [Zigbee2MQTT Device Page](https://www.zigbee2mqtt.io/devices/501.40.html)). Ensure your remote is correctly paired with Zigbee2MQTT.
  * **Topic Configuration:** You will need to configure the correct MQTT topic for your remote in the blueprint inputs.

## Features

This blueprint supports the following features of the Paulmann Gent 2 RGB remote:

  * **On/Off Control:** Toggles the power state of the configured lights using the on/off buttons. (cf. limitations below for the implications)
  * **Brightness Adjustment:**  Increases and decreases brightness in steps using dedicated brightness up/down buttons.
  * **Color Temperature Control:** Sets predefined color temperatures (Warm White, Neutral White, Cold White) using the dedicated color temperature buttons (Fire, Play, Snowflake icons).
  * **Color Selection from Color Wheel:** Allows you to select colors using the remote's color wheel, controlling the hue and saturation of your lights.
  * **Scene Recall:**  Supports recalling up to 7 pre-configured lighting scenes with custom colors and brightness levels, accessible via the 7 scene buttons on the remote.
  * **Transition Time:**  Allows you to configure a transition time for smooth changes in brightness, color, and color temperature.
  * **Control of Multiple Light Groups:**  Provides configuration for controlling up to four different groups of lights, each assigned to a dedicated button on the remote.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](upload://3IEiMyDuriGlhMmaFV0iSnXlL0b.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fbuz-ch%2Fpaulmann_gent2%2Fblob%2Fmain%2Fpaulmann_gent2.yaml)


## Limitations

The following limitations are currently present in this blueprint:

  * **Discrete Color Temperatures:** The color temperature buttons set predefined, discrete color temperatures (Warm, Neutral, Cold) rather than providing continuous color temperature adjustment. This saves us from weird hacks qand/or input helpers
  * **Saturation Button (Center of Color Wheel) Unsupported:** The function of the saturation button in the middle of the color wheel is currently unclear and therefore not implemented.
  * **`color_move` Not Implemented:** Continuous color movement functionality (`color_move` command) is not implemented as its behavior with multiple lights is ambiguous.
  * **`light.toggle` for On/Off:** The blueprint uses `light.toggle` for on/off commands because the remote does not report light state. This means that if lights in a group have differing states, `toggle` might produce unexpected results.  For consistent on/off behavior across a group, ensure all lights in the group are in the same state before using the on/off buttons.
  * **Limited Testing:** The blueprint has primarily been tested with Philips Hue Color Lights (LCA004 and Infuse). Functionality with other light brands and types, especially non-RGB lights, is not guaranteed and may be limited or nonsensical.

## Inputs

Before using this blueprint, you need to configure the following inputs:

| Input Variable                  | Description                                                                                                                                                                                                                            | Default Value            | Selector Type   |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | --------------- |
| **`mqtt_topic`**                  | The base MQTT topic for your Paulmann Gent 2 remote in Zigbee2MQTT. This is usually in the format `zigbee2mqtt/{FRIENDLY_NAME}`, where `{FRIENDLY_NAME}` is the friendly name you assigned to your remote in Zigbee2MQTT. | `"zigbee2mqtt/"`        | Text            |
| **`target_lights_1`**             | Lights to control with button 1 on the remote. You can select individual lights or light groups.                                                                                                                                     | `[]` (No lights selected) | Target (Light Entity) |
| **`target_lights_2`**             | Lights to control with button 2 on the remote.                                                                                                                                                                                          | `[]` (No lights selected) | Target (Light Entity) |
| **`target_lights_3`**             | Lights to control with button 3 on the remote.                                                                                                                                                                                          | `[]` (No lights selected) | Target (Light Entity) |
| **`target_lights_4`**             | Lights to control with button 4 on the remote.                                                                                                                                                                                          | `[]` (No lights selected) | Target (Light Entity) |
| **`transition_time`**             | The time (in seconds) for transitions between light states (brightness, color, color temperature changes).                                                                                                                               | `5` seconds                | Number (Range 0-1800) |
| **`color_temperature_warm_white`**  | The color temperature (in Kelvin) to set when the "Warm White" (Fire icon) button is pressed.                                                                                                                                     | `2700` Kelvin             | Number (Range 2000-9000) |
| **`color_temperature_neutral_white`** | The color temperature (in Kelvin) to set when the "Neutral White" (Play icon) button is pressed.                                                                                                                                  | `4000` Kelvin             | Number (Range 2000-9000) |
| **`color_temperature_cold_white`**   | The color temperature (in Kelvin) to set when the "Cold White" (Snowflake icon) button is pressed.                                                                                                                                  | `6000` Kelvin             | Number (Range 2000-9000) |
| **`scene_1_color`** - **`scene_7_color`** |  RGB color for scene 1 through scene 7, respectively. Configures the color recalled when the corresponding scene button is pressed.                                                                                             | `[255, 255, 255]` (White)  | Color RGB       |
| **`scene_1_brightness`** - **`scene_7_brightness`** | Brightness level (percentage 1-100) for scene 1 through scene 7, respectively. Configures the brightness recalled when the corresponding scene button is pressed.                                                       | `80` (%)                   | Number (Range 1-100) |

## Installation and Usage

1.  **Prerequisites:**
      * **Home Assistant:** Ensure you have a working Home Assistant installation.
      * **Zigbee2MQTT:**  You must be using Zigbee2MQTT to manage your Zigbee devices.
      * **Paulmann Gent 2 RGB Remote (or compatible):**  Your remote must be paired with Zigbee2MQTT and be correctly publishing MQTT messages. Verify this by listening to the MQTT topic in your Zigbee2MQTT interface or using an MQTT client.
2.  **Import the Blueprint:**
      * In your Home Assistant UI, go to **Configuration** -\> **Blueprints**.
      * Click the "**Import Blueprint**" button (bottom right).
      * Paste the YAML code of this blueprint directly into the import dialog and click "**Preview Blueprint**".
      * Click "**Import Blueprint**".
3.  **Create an Automation from the Blueprint:**
      * Once the blueprint is imported, click "**Create Automation**".
      * **Configure Inputs:** Fill in the input variables as described in the "Inputs" section above.
          * **`mqtt_topic`:**  Enter the correct MQTT topic for your remote (e.g., `zigbee2mqtt/MyRemoteName`).
          * **`target_lights_1` - `target_lights_4`:** Select the light entities or light groups you want to control with each button.
          * **`transition_time`:** Adjust the transition time if desired.
          * **`color_temperature_*_white`:** Customize the color temperatures for the dedicated buttons if needed.
          * **`scene_*_color` and `scene_*_brightness`:** Define the colors and brightness levels for each of the 7 scenes.
      * Click "**Save**" to save your automation.
4.  **Test:**  Test the remote buttons to ensure they control your lights as expected. Monitor the MQTT topic in your Zigbee2MQTT interface or Home Assistant logs for any errors or unexpected behavior.

## Areas for Improvement and Future Work

The following areas could be improved in future versions of this blueprint:

  * **Color Temperature Step Logic Verification and Correction:**  Verify and potentially correct the logic of the color temperature step buttons (`color_temperature_step_up`/`_down`) to ensure they control color temperature in the intended warmer/cooler direction.
  * **Implement `hue_move` Functionality:** Fully implement continuous hue control (`hue_move` command) from the color wheel.
  * **Investigate Saturation Button Behavior:**  Further investigate the function and potential implementation of the saturation button (center of the color wheel).
  * **Error Handling:** Implement more robust error handling and logging within the automation for potential issues.
  * **Clarify Color Temperature Button Behavior in Documentation:**  Improve documentation clarity regarding the discrete nature of the color temperature buttons and their configured Kelvin values.
  * **Consider "Stop" Action for Movement Commands:** If continuous movement commands like `hue_move` or `color_move` are implemented, add a "stop" action triggered by button release.

## Disclaimer

This software is provided "as is," without any warranties or guarantees of any kind, express or implied, including but not limited to warranties of merchantability, fitness for a particular purpose, or non-infringement. The authors and distributors shall not be liable for any damages arising from the use, inability to use, or performance of this software. Use at your own risk.

