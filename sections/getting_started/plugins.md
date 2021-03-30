# Plugins

BDSX provides the functionality to load BDSX plugins and DLL mods.

## Finding and Installing Plugins

You can find and install plugins using `npm run plugin-manager` in the BSDX directory.

## Private Plugins

You can download private plugins provided by others to the "plugins" folder located in the BDSX directory and they will be loaded on the next launch.

## C++ Mods

Some C++ mods conflict with BDSX, [BDSNetRunner](https://github.com/zhkj-liuxiaohua/BDSNetRunner) is a known one, but you can still load them by putting the following code in "index.ts" located in the BDSX directory.
```ts
    import { NativeModule } from "bdsx";
    NativeModule.load("filename.dll");
```