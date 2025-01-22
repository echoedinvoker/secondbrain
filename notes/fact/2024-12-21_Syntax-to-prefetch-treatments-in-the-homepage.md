---
date: 2024-12-21
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Syntax to prefetch treatments in the homepage

After we have [[2024-12-21_Plan-to-prefetch-treatments-in-the-homepage|plan to prefetch treatments in the homepage]], now we can start to implement it.

```ts
// src/components/treatments/hooks/useTreatments.ts 

import type { Treatment } from "@shared/types";

import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";
import { useQuery, useQueryClient } from "@tanstack/react-query";

async function getTreatments(): Promise<Treatment[]> {
  const { data } = await axiosInstance.get('/treatments');
  return data;
}

export function useTreatments(): Treatment[] {
  const fallback: Treatment[] = []
  const { data = fallback } = useQuery({
    queryKey: [queryKeys.treatments],
    queryFn: getTreatments
  })

  return data;
}

// create a new hook to prefetch the treatments 
export function usePrefetchTreatments() {
  const queryClient = useQueryClient() // use hook to get the queryClient instance from provider
  // prefetch the treatments
  queryClient.prefetchQuery({
    // queryKey and queryFn are the same as the useQuery hook above, this is important
    queryKey: [queryKeys.treatments],
    queryFn: getTreatments,
    // you can add more options here, such as longer gcTime
  })
}
```

```tsx
// src/components/app/Home.tsx 

import { Icon, Stack, Text } from "@chakra-ui/react";
import { GiFlowerPot } from "react-icons/gi";

import { BackgroundImage } from "@/components/common/BackgroundImage";
import { usePrefetchTreatments } from "../treatments/hooks/useTreatments"; // import

export function Home() {

  usePrefetchTreatments() // because usePrefetchTreatments is a hook, you can't use it in the useEffect hook but you can use it directly in the component
                          // Home component won't be re-rendered frequently, so it's safe to call usePrefetchTreatments directly in the component

  return (
    <Stack textAlign="center" justify="center" height="84vh">
      <BackgroundImage />
      <Text textAlign="center" fontFamily="Forum, sans-serif" fontSize="6em">
        <Icon m={4} verticalAlign="top" as={GiFlowerPot} />
        Lazy Days Spa
      </Text>
      <Text>Hours: limited</Text>
      <Text>Address: nearby</Text>
    </Stack>
  );
}

```

![prefetch-in-homepage.png](../assets/imgs/prefetch-in-homepage.png)

![refresh-without-loading.png](../assets/imgs/refresh-without-loading.png)
