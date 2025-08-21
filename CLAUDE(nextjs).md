## Next.js Page Component Separation Guide

Structure for efficiently separating long page.tsx files by page.

- Basic Structure

```
app/
├── dashboard/
│   ├── page.tsx              # Main page
│   ├── components/           # Page-specific components
│   │   ├── DashboardHeader.tsx
│   │   ├── StatsCard.tsx
│   │   └── index.ts          # Barrel export
│   ├── hooks/                # Page-specific hooks
│   │   └── useDashboardData.ts
│   └── types.ts              # Page-specific types
└── components/               # Global shared components
```

- Separation Process

1. Extract Components by Section

**Before: Long page.tsx**

```typescript
export default function DashboardPage() {
  return (
    <div className="min-h-screen">
      {/* 200+ lines of complex JSX */}
      <header>{/* header logic */}</header>
      <main>{/* stats, activity feed, charts, etc. */}</main>
    </div>
  );
}
```

**After: Separated Structure**

```typescript
import { DashboardHeader, StatsOverview, ActivityFeed } from "./components";

export default function DashboardPage() {
  return (
    <div className="min-h-screen">
      <DashboardHeader />
      <main className="container mx-auto py-6">
        <StatsOverview />
        <ActivityFeed />
      </main>
    </div>
  );
}
```

2. Create Individual Components

```typescript
// app/dashboard/components/DashboardHeader.tsx
export function DashboardHeader() {
  return (
    <header className="bg-white shadow">
      <div className="container mx-auto px-4 py-4">
        <h1 className="text-2xl font-bold">Dashboard</h1>
      </div>
    </header>
  );
}
```

3. Organize imports with Barrel Exports

```typescript
// app/dashboard/components/index.ts
export { DashboardHeader } from "./DashboardHeader";
export { StatsOverview } from "./StatsOverview";
export { ActivityFeed } from "./ActivityFeed";
```

- Separate Logic with Custom Hooks

```typescript
// app/dashboard/hooks/useDashboardData.ts
export function useDashboardData() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchData()
      .then(setData)
      .finally(() => setLoading(false));
  }, []);

  return { data, loading };
}

// Usage in app/dashboard/page.tsx
export default function DashboardPage() {
  const { data, loading } = useDashboardData();

  if (loading) return <Loading />;

  return (
    <div>
      <DashboardHeader />
      <StatsOverview data={data} />
    </div>
  );
}
```

- Separation Criteria

**Component Separation Targets:**

- JSX blocks with 50+ lines
- Sections with independent state
- Reusable UI patterns

**Hook Separation Targets:**

- API call logic
- Complex state management
- Form validation

- Performance Optimization

1. Dynamic Imports

```typescript
import dynamic from "next/dynamic";

const HeavyChart = dynamic(() => import("./components/HeavyChart"), {
  loading: () => <div>Loading...</div>,
});
```

2. Memoization

```typescript
import { memo } from "react";

export const StatsCard = memo(function StatsCard({ title, value }) {
  return (
    <div className="bg-white p-4 rounded shadow">
      <h3>{title}</h3>
      <p className="text-2xl font-bold">{value}</p>
    </div>
  );
});
```

- Checklist

- [ ] Create `components/` folder
- [ ] Create `hooks/` folder (if needed)
- [ ] Add `index.ts` barrel export
- [ ] Separate JSX blocks with 50+ lines
- [ ] Extract API logic into custom hooks
