---
date: 2025-05-13
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Crazy TypeSafe use case! (key_value pairs and generics)

Try to optimize the code below to be type safe and remove duplicated code.
```ts
import { ref, watch } from 'vue'

interface GeneralSettings {
  username: string
  email: string
  about: string
  gender: string
  country: string
}

const general = ref<GeneralSettings>(
  (() => {
    const stored = localStorage.getItem('general')
    return stored !== null
      ? JSON.parse(stored)
      : {
          about: '',
          country: 'USA',
          gender: 'male',
          email: '',
          username: '',
        }
  })(),
)

watch(general, (value) => localStorage.setItem('general', JSON.stringify(value)), { deep: true })

interface NotificationsSettings {
  email: boolean
  sms: boolean
}

const notifications = ref<NotificationsSettings>(
  (() => {
    const stored = localStorage.getItem('notifications')
    return stored !== null
      ? JSON.parse(stored)
      : {
          email: false,
          sms: false,
        }
  })()
)

watch(notifications, (value) => localStorage.setItem('notifications', JSON.stringify(value)), { deep: true })

interface PrivacySettings {
  visbility: Visbility
  searchEngineIndexing: boolean
}

type Visbility = 'public' | 'private'

const privacy = ref<PrivacySettings>(
  (() => {
    const stored = localStorage.getItem('privacy')
    return stored !== null
      ? JSON.parse(stored)
      : {
          visbility: 'private',
          searchEngineIndexing: false,
        }
  })()
)

watch(privacy, (value) => localStorage.setItem('privacy', JSON.stringify(value)), { deep: true })

export function useSettings() {
  return {
    general,
    notifications,
    privacy,
  }
}
```

First, we can extract the IIFE function that initializes the different settings into a reusable function. And we can see that this function need two parameters: the key to store the settings in localStorage and the default value to use, and these two parameters are related to each other, so we can define their type as below:

```ts
interface SettingsMap { // type for default values
  general: GeneralSettings
  notifications: NotificationsSettings
  privacy: PrivacySettings
  //       ^^^^^^^^^^^^^^^ point to the interface of the default value
}

type SettingsKeys = keyof SettingsMap // type for keys, in this case: 'general' | 'notifications' | 'privacy'
```

Then we can define the function with type safety:

```ts
const init = <T extends SettingsKeys>(key: T, defaultValue: SettingsMap[T]) => {
  const stored = localStorage.getItem(key)
  return stored !== null ? JSON.parse(stored) : defaultValue
}
```

And use it to initialize the different settings, here we only use `general` as an example:

```ts
const general = ref<GeneralSettings>(
  init('general', {
    about: '',
    country: 'USA',
    gender: 'male',
    email: '',
    username: '',
  }),
)
```

The magical thing about type safety above is that key and defaultValue are interdependent. If 'general' is used but defaultValue is NotificationsSettings, TypeScript will throw an error, and vice versa.

Next, we can also extract the watch function into a reusable function with type safety:

```ts
const watcher = <T extends SettingsKeys>(key: T) =>
  (value: SettingsMap[T]) => {
    localStorage.setItem(key, JSON.stringify(value))
  };
```

You can see above function returns a function, first parameter is the key used to tell which type of settings to watch, and the second parameter is the value of the settings to watch. The type of the value is also interdependent with the key, so if you use 'general' but pass in NotificationsSettings, TypeScript will throw an error.

Finally, we can use it to watch the different settings, let's say we want to watch the `general` settings:

```ts
watch(general, watcher('general'), { deep: true })
//                     ^^^^^^^^^ specify the key `general` to watch, so if we pass ref 'notifications' to the first parameter of watch, TypeScript will throw an error, this is very mind-blowing!
```

## Conclusion

This case is really mind-blowing, it uses the power of TypeScript to make the code extremely type safe, and the code is very clean and reusable. The key point is to use the interdependent types to make sure that the key and default value are related to each other, and the key and value in the watch function are also related to each other. This is a very powerful feature of TypeScript, and it can be used in many different scenarios.




