# shopware/frontends - api-client

[![](https://img.shields.io/npm/v/@shopware/api-client?color=blue&logo=data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTIiIGhlaWdodD0iMTIiIHZpZXdCb3g9IjAgMCA0ODggNTUzIiBmaWxsPSJub25lIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciPgo8cGF0aCBkPSJNNDM5LjA0MSAxMjkuNTkzTDI1OC43NjkgMzEuMzA3NkMyNDQuOTE1IDIzLjc1NDEgMjI4LjExNiAyNC4wMDkzIDIxNC40OTcgMzEuOTgwMkw0Ny4yNjkgMTI5Ljg1OEMzMy40NzYzIDEzNy45MzEgMjUgMTUyLjcxMyAyNSAxNjguNjk1VjM4OC40NjZDMjUgNDA0LjczMiAzMy43Nzg1IDQxOS43MzIgNDcuOTYwMiA0MjcuNjk5TDIxNS4xNzggNTIxLjYzNkMyMjguNDUxIDUyOS4wOTIgMjQ0LjU5MyA1MjkuMzMyIDI1OC4wODIgNTIyLjI3NEw0MzguMzY0IDQyNy45MzRDNDUzLjIwMSA0MjAuMTcgNDYyLjUgNDA0LjgwOSA0NjIuNSAzODguMDYzVjE2OS4xMDJDNDYyLjUgMTUyLjYzMiA0NTMuNTAyIDEzNy40NzcgNDM5LjA0MSAxMjkuNTkzWiIgc3Ryb2tlPSJ1cmwoI3BhaW50MF9saW5lYXJfMTUzXzY5MjY1KSIgc3Ryb2tlLXdpZHRoPSI1MCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCIvPgo8ZGVmcz4KPGxpbmVhckdyYWRpZW50IGlkPSJwYWludDBfbGluZWFyXzE1M182OTI2NSIgeDE9Ii0xNi4yOTg5IiB5MT0iMTY1LjM0OSIgeDI9IjI3Ni40MTIiIHkyPSItODkuMzIzNCIgZ3JhZGllbnRVbml0cz0idXNlclNwYWNlT25Vc2UiPgo8c3RvcCBzdG9wLWNvbG9yPSIjMDA4NUZGIi8+CjxzdG9wIG9mZnNldD0iMSIgc3RvcC1jb2xvcj0iI0MwRTJGNSIvPgo8L2xpbmVhckdyYWRpZW50Pgo8L2RlZnM+Cjwvc3ZnPg==)](https://npmjs.com/package/@shopware/api-client)
[![](https://img.shields.io/github/package-json/v/shopware/frontends?color=blue&filename=packages%2Fapi-client-next%2Fpackage.json&label=frontends/api-client&logo=github)](https://github.com/shopware/frontends/tree/main/packages/api-client-next)
![](https://img.shields.io/github/license/shopware/frontends?color=blue)
[![](https://img.shields.io/github/issues/shopware/frontends/api-client?label=package%20issues&logo=github)](https://github.com/shopware/frontends/issues?q=is%3Aopen+is%3Aissue+label%3Aapi-client)

Dynamic and fully typed API Client for Shopware 6. Usable in any JavaScript an TypeScript project.
You can use types generated from your custom API instance to have autocompletion and type safety.

## Setup

Install npm package:

```bash
# Using pnpm
pnpm add @shopware/api-client

# Using yarn
yarn add @shopware/api-client

# Using npm
npm i @shopware/api-client
```

Recommended practice is to create separate module file. For example `src/apiClient.ts`, and import it whenever you need to use API Client.

```typescript
import { createAPIClient } from "@shopware/api-client";

// You can pick types of your current API version, the default one:
import {
  operationPaths,
  operations,
  components,
} from "@shopware/api-client/api-types";
// or (specific version):
import {
  operationPaths,
  operations,
  components,
} from "@shopware/api-client/api-types/apiTypes-6.4.20.0";
// or your types generated by @shopware/api-gen CLI:
import { operationPaths, operations, components } from "./apiTypes";

// you can pick cookies library of your choice
import Cookies from "js-cookie";

export const apiClient = createAPIClient<operations, operationPaths>({
  baseURL: "https://demo-frontends.shopware.store/store-api",
  accessToken: "SWSCBHFSNTVMAWNZDNFKSHLAYW",
  contextToken: Cookies.get("sw-context-token"),
  apiType: "store-api",
  onContextChanged(newContextToken) {
    Cookies.set("sw-context-token", newContextToken, {
      expires: 365, // days
      path: "/",
      sameSite: "lax",
    });
  },
});

// reimport schemas to use it in application
export type ApiSchemas = components["schemas"];
// reimport operations request parameters to use it in application
export type ApiRequestParams<OPERATION_NAME extends keyof operations> =
  RequestParameters<OPERATION_NAME, operations>;
// reimport operations return types to use it in application
export type ApiReturnType<OPERATION_NAME extends keyof operations> =
  RequestReturnType<OPERATION_NAME, operations>;
```

## Basic usage

Take a look at [example project using API Client](https://stackblitz.com/github/shopware/frontends/tree/main/examples/new-api-client).

### Simple invocation

```typescript
import { apiClient, ApiReturnType } from "./apiClient";

// could be reactive value, you can use ApiReturnType to type it properly
let productsResponse: ApiReturnType<"readProduct">;

async function loadProducts() {
  productsResponse = await apiClient.invoke("readProduct post /product", {
    limit: 2,
  });
}
```

### Predefining methods

If you prefer to add another layer of abstraction you can use created previously types to define your own concept of methods.

```typescript
// add for example into apiClient.ts file
const readNavigation = (params: ApiRequestParams<"readNavigation">) =>
  apiClient.invoke(
    "readNavigation post /navigation/{activeId}/{rootId} sw-include-seo-urls",
    {
      depth: 2,
      ...params,
    }
  );

// in another file you can use it, and depth property will be set to 2 by default
import { readNavigation } from "./apiClient";

async function loadMainNavigation() {
  const navigation = await readNavigation({
    activeId: "main-navigation",
    rootId: "main-navigation",
  });
}
```

## Links

- [📘 Documentation](https://frontends.shopware.com)

- [👥 Community Slack](https://shopwarecommunity.slack.com) (`#shopware-frontends` channel)

<!-- AUTO GENERATED CHANGELOG -->

## Changelog

Full changelog for stable version is available [here](https://github.com/shopware/frontends/blob/main/packages/api-client-next/CHANGELOG.md)

### Latest changes: 0.0.3

### Patch Changes

- [#290](https://github.com/shopware/frontends/pull/290) [`9562a8a`](https://github.com/shopware/frontends/commit/9562a8add35751093d766017abba474f0ad578f8) Thanks [@patzick](https://github.com/patzick)! - ship api-types with package