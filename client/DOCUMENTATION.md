## Client

This is where your Nuxt application is stored.

## Installation

The whole installation process is to create a shared network, build containers and initialize a new Nuxt application.

Run the installation script in your terminal, and it will do it all automatically:

```bash
# Nuxt 3
./install
```

You can also install the Nuxt 2 version:

```bash
# Nuxt 2
./install --nuxt-2
```

Now you should be able to see it running in your browser at [http://localhost:3000](http://localhost:3000).

> After installation the `stubs` directory and `install` script may be deleted.

## Installation to an existing project

1. Copy all files from the `client` directory to your application directory.
2. Create the `.env` file from `.env.dev`.
3. Build and run containers using the `make up` command.

## Usage

Most common docker commands are abstracted into [Makefile](./Makefile) instructions.

They are basic and often just instead of the `docker compose` command you need to write `make` in your terminal.

Feel free to explore them and edit them according to your needs.

### Start containers

```bash
# Make command
make up

# Full command
docker-compose -f docker-compose.dev.yml up -d
```

Now you can open the [http://localhost:3000](http://localhost:3000) URL in your browser.

### Stop containers

```bash
# Make command
make down

# Full command
docker-compose -f docker-compose.dev.yml down
```

### Fetch API data

To fetch data from the Laravel API, you have to use different endpoints when requesting data from the browser and during the SSR process from the Node server instance.

The `.env` file already contains those endpoints, so you can use the following `nuxt.config.ts` file:

```ts
import { defineNuxtConfig } from 'nuxt'

export default defineNuxtConfig({
  publicRuntimeConfig: {
    apiUrlBrowser: process.env.API_URL_BROWSER,
  },

  privateRuntimeConfig: {
    apiUrlServer: process.env.API_URL_SERVER,
  }
})
```

Then, you can create something like this composable function `/composables/apiFetch.ts`:

```ts
import type { FetchOptions } from 'ohmyfetch'

export const useApiFetch = (path: string, opts?: FetchOptions) => {
  const config = useRuntimeConfig()

  return $fetch(path, {
    baseURL: process.server ? config.apiUrlServer : config.apiUrlBrowser,
    ...(opts && { ...opts })
  })
}
```

Now in the component file you can use it as following:

```vue
<script setup>
const { data } = await useApiFetch('/products')
</script>
```

#### Nuxt 2

For Nuxt 2 app you need to configure `axios` in the `nuxt.config.js` file like this:

```js
{
  publicRuntimeConfig: {
    axios: {
      browserBaseURL: process.env.API_URL_BROWSER
    }
  },

  privateRuntimeConfig: {
    axios: {
      baseURL: process.env.API_URL_SERVER
    }
  }
}
```

Now in the component file you can use it like this:

```vue
<script>
export default {
  async asyncData ({ $axios }) {
    const { data } = await $axios.$get('/products')

    return {
      products: data
    }
  }
}
</script>
```
