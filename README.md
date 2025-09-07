# Minecraft map colors

This repository contains a list of all available map colors for each version between 1.8.3 and 1.21.8. The colors were fetched by a script that pulled
the Spigot source code for each version and scanned the MaterialMapColor.java class.

[Click me to go to the color index](index.md)

[Click me to view the changes between the versions](changes.md)

## Why did you make this repository?

I work a lot with maps and figuring out which colors work for a specific Minecraft version has always been a time-consuming process for me. That's why
I decided to make this repository.

## Contents

- `index.md`: An index for each version stored in this repository
- `versions/`: The directory that contains the versions
- `mappings.json`: All of the colors in a convenient json file
- `mappings_min.json`: Same as above but in a way smaller format

## mappings.json

Mappings format:

```json5
// Root
{
  // Version item
  "1.8.8": {
    // Color item
    // Color 0 and all of its variants is always completely transparent.
    "0": {
      // The base color
      base: 0,
      // The variants of this color
      colors: [
        0,
        0,
        0,
        0
      ]
    },
    // Another color item
    "1": {
      // The base color
      "base": 8368696,
      // The variants of this color
      "colors": [
        5864743,
        7182640,
        8368696,
        4415005
      ]
    },
    // ...more colors
  },
  // ...more versions
}
```

Example: Reading the mappings in Java with Gson

```java
import com.google.gson.JsonArray;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import java.awt.Color;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;

class MyClass {

    public static void main(String[] args) {
        // Load the mappings
        File mappingsFile = new File("./mappings.json");
        JsonObject obj;
        try (FileInputStream in = new FileInputStream(mappingsFile);
             InputStreamReader reader = new InputStreamReader(in)) {
            obj = JsonParser.parseReader(reader).getAsJsonObject();
        } catch (IOException ex) {
            throw new RuntimeException(ex);
        }

        // Get the colors of MC 1.16.5
        final JsonObject version = obj.get("1.16.5").getAsJsonObject();
        // Loop through the colors of MC 1.16.5
        for (String key : version.keySet()) {
            final JsonObject colorObj = version.get(key).getAsJsonObject();
            final JsonArray colorArr = colorObj.get("colors").getAsJsonArray();
            // The key is always the color id
            int id = Integer.parseInt(key);
            for (int variant = 0; variant < colorArr.size(); variant++) {
                // Get the color and deserialize it with java.awt.Color
                int rgb = colorArr.get(variant).getAsInt();
                final Color color = new Color(rgb, false /*<- Important!*/);
                // id * 4 + variant = Protocol ID
                System.out.printf("%d = rgb(%d, %d, %d)%n", id * 4 + variant, color.getRed(), color.getGreen(), color.getBlue());

                // Output:
                // 0 = rgb(0, 0, 0)
                // 1 = rgb(0, 0, 0)
                // 2 = rgb(0, 0, 0)
                // 3 = rgb(0, 0, 0)
                // 4 = rgb(89, 125, 39)
                // 5 = rgb(109, 153, 48)
                // 6 = rgb(127, 178, 56)
                // 7 = rgb(67, 94, 29)
                // ...
            }
        }
    }
}
```

## mappings_min.json

The basic format is the same as in mappings.json. The only difference is that colors that have not changed have not been recorded in the file.

## Enjoy my work?

[:heart: Sponsor me on GitHub](https://github.com/sponsors/cerus)