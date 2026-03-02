# Expo + React Native Rules

## 🎯 Purpose
Core conventions for an Expo React Native project — covering project setup, navigation, styling, and the most common cross-platform pitfalls.

## 📋 Context
- Expo SDK 51+ (managed workflow preferred)
- Expo Router v3 for file-based navigation
- NativeWind for Tailwind-style utility classes
- TypeScript strict mode

## 💬 The Prompt / Rules

```
You are building a React Native application with Expo and Expo Router.
Follow these rules for all React Native development.

**Project Setup**
Use: npx create-expo-app@latest {app_name} --template blank-typescript
Install immediately:
- expo-router — file-based navigation
- nativewind — Tailwind for React Native
- zustand — global state
- @tanstack/react-query — server state
- zod — validation

**Expo Router — File Structure**
app/
  _layout.tsx       — root layout (fonts, providers)
  (tabs)/           — tab group
    _layout.tsx     — tab bar config
    index.tsx       — home tab
    profile.tsx     — profile tab
  (auth)/
    login.tsx
    register.tsx
  [id].tsx          — dynamic route

**Navigation Rules**
- Always use typed routes with expo-router
- Use <Link> for declarative navigation, router.push() for imperative
- Avoid deep nesting — max 3 levels of navigation
- Pass minimal data through navigation — fetch by ID on the destination screen
- Use route params only for IDs, not for large objects

**Platform Differences**
- Use Platform.OS to conditionally apply platform-specific styles
- Platform-specific files: Component.ios.tsx, Component.android.tsx
  (prefer conditional logic over separate files for small differences)
- Test on both iOS and Android — don't assume web behavior works

**Styling with NativeWind**
- Same Tailwind classes as web (mostly)
- Use className prop — NativeWind maps it to StyleSheet
- SafeAreaView for top/bottom padding: wrap root screen views
- Avoid margin/padding on the outermost View — use SafeAreaView insets

**Differences from Web React**
- No CSS, no DOM — use StyleSheet or NativeWind
- No <div> → <View>, no <p> → <Text>, no <img> → <Image>
- All Text must be inside a <Text> component — not loose text nodes
- Touchable elements: use <Pressable> (not <button>)
- Keyboard: handle with KeyboardAvoidingView on forms
- ScrollView: use FlatList for long lists — never ScrollView with .map()

**FlatList Rules**
Always use FlatList for lists (not .map in ScrollView):
- keyExtractor: return item.id (must be a string)
- renderItem: extract into a separate component for performance
- Use getItemLayout when items have fixed height (huge performance win)
- ListEmptyComponent: always provide one

**Images**
- Use expo-image (not React Native's Image) for better performance and caching
- Always specify width and height — no auto-sizing
- contentFit="cover" for thumbnails, contentFit="contain" for logos

**State Management**
- Same Zustand + TanStack Query approach as web
- Persist user preferences with MMKV (faster than AsyncStorage)
  zustand persist with MMKV storage adapter

**Performance**
- memo() component if it's in a FlatList
- Avoid anonymous functions in JSX for FlatList items
- Use Hermes engine (default in Expo) — it handles most perf automatically
- Profile with Flashlight before optimizing — don't guess

**Permissions**
- Request permissions at the point of use — not at app launch
- Always handle denied permissions gracefully with a fallback UI
- Use expo-permissions or the specific expo module (expo-camera, expo-location)

**Build and Distribution**
- Use EAS Build for all builds — not local builds
- Use EAS Update for OTA updates (JS changes only)
- Never commit .env — use EAS Secrets for CI/CD env vars
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{app_name}` | Application name | `my-mobile-app` |
| `{platform}` | Target platform | `iOS` / `Android` / `both` |

## 📝 Example Output

```typescript
// app/(tabs)/index.tsx
import { FlatList, View } from 'react-native'
import { SafeAreaView } from 'react-native-safe-area-context'
import { PostCard } from '@/components/PostCard'
import { usePosts } from '@/hooks/usePosts'

export default function HomeScreen() {
  const { data: posts, isLoading } = usePosts()

  return (
    <SafeAreaView className="flex-1 bg-background">
      <FlatList
        data={posts}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => <PostCard post={item} />}
        ListEmptyComponent={isLoading ? <LoadingSkeleton /> : <EmptyState />}
        contentContainerClassName="p-4 gap-3"
      />
    </SafeAreaView>
  )
}
```

## 🔄 Variations

- **Bare Workflow:** Add notes on native module setup and rebuild requirements
- **With Auth:** Add Expo SecureStore for token storage

---

*Last updated: 2026-03-02*
