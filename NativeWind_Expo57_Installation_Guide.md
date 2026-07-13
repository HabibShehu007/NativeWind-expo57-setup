# NativeWind v4 Installation Guide (Expo SDK 57)

This guide explains how to manually add **NativeWind v4** to an **existing Expo SDK 57** project using **npm**.

> **Assumptions**
>
> - Existing Expo SDK 57 project
> - Expo Router + TypeScript
> - Using npm

---

## 1. Install Dependencies

Install NativeWind and Tailwind CSS.

```bash
npm install nativewind
npm install --save-dev tailwindcss@3.4.17
```

Verify:

```bash
npm ls nativewind tailwindcss
```

Expected output in your terminal:

```text
nativewind@4.2.6
tailwindcss@3.4.17
```

---

## 2. Generate Tailwind Configuration

Generate the configuration for tailwind which is tailwind.config.js file.

```bash
npx tailwindcss init
```

Replace `tailwind.config.js` with:

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  presets: [require("nativewind/preset")],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

---

## 3. Create `global.css`

Create `global.css` in the project root.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## 4. Configure Babel (Critical)

Create `babel.config.js`.

```js
module.exports = function (api) {
  api.cache(true);

  return {
    presets: [
      ["babel-preset-expo", { jsxImportSource: "nativewind" }],
      "nativewind/babel",
    ],
  };
};
```

> **Important**
>
> For Expo SDK 57, `nativewind/babel` belongs in **presets**.
>
> If placed inside `plugins`, you'll likely see:
>
> `.plugins is not a valid Plugin property`

---

## 5. Generate and modify Metro.config.js (Critical)

```bash
 npx expo customize metro.config.js
```

This will generate new `metro.config.js`. with the default presets replace whatever you found with

```js
const { getDefaultConfig } = require("expo/metro-config");
const { withNativeWind } = require("nativewind/metro");

const config = getDefaultConfig(__dirname);

module.exports = withNativeWind(config, {
  input: "./global.css",
});
```

---

## 6. Import `global.css`

If your routes live in `src/app`:

```tsx
import "../../global.css";
```

Mostly inside:

```text
src/app/_layout.tsx
```

---

## 7. Restart Metro

```bash
npx expo start --clear
```

Always clear Metro after changing Babel, Metro or Tailwind configuration.

---

## 8. Test this minimal tailwindCss Styles to Verify it's working

```Text
copy and paste inside src/app/index.tsx
```

```tsx
import { View, Text, Pressable } from "react-native";

export default function HomeScreen() {
  return (
    <View className="flex-1 bg-slate-100 justify-center items-center px-6">
      <View className="w-full rounded-[32px] bg-[#414BE5] p-8 shadow-lg">
        <Text className="text-white text-3xl font-bold">Good Morning 👋</Text>

        <Text className="text-indigo-100 mt-2 text-base">
          NativeWind is now configured correctly.
        </Text>

        <Pressable className="mt-8 bg-white rounded-2xl py-4 items-center active:opacity-80">
          <Text className="text-[#414BE5] font-bold text-lg">Continue</Text>
        </Pressable>
      </View>
    </View>
  );
}
```

---

# Common Issues

## `.plugins is not a valid Plugin property`

**Cause**

- `nativewind/babel` configured under `plugins`.

**Solution**

Move it into `presets` as shown above.

---

## Styles are not applied

Check:

- `global.css`
- `tailwind.config.js`
- Metro cache

```bash
npx expo start --clear
```

---

## Git Notes

Ignore:

- `node_modules/`
- `.expo/`
- `expo-env.d.ts`

Commit:

- `babel.config.js`
- `metro.config.js`
- `tailwind.config.js`
- `global.css`
- `package.json`
- `tsconfig.json`
