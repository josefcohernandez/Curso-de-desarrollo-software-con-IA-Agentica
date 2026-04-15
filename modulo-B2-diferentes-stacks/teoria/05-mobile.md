# IA Agéntica y Mobile (React Native, Flutter)

## La diferencia clave: el agente no controla el simulador

En frontend web, abres el navegador y ves el resultado. En mobile, la situación tiene matices importantes (actualizado a 2026):

- **Claude Code puede recibir y analizar screenshots** gracias a su capacidad multimodal. Puedes pegar o arrastrar capturas de pantalla directamente en la conversación y el agente las interpreta.
- **Computer Use** (preview, solo macOS) permite a Claude capturar screenshots automáticamente del escritorio.
- **Sin embargo**, el agente **no puede lanzar autónomamente un simulador mobile, interactuar con él ni capturar pantallas del simulador** por su cuenta. No puede tocar botones, hacer scroll ni navegar por la app.

En la práctica, tú sigues siendo los "ojos" del agente en la mayoría de workflows mobile estándar: ejecutas la app en el simulador, capturas la pantalla y se la envías al agente para que itere. El workflow visual iterativo que describimos a continuación sigue siendo la forma más efectiva de trabajar.

## El workflow visual iterativo

1. **Descripción**: proporcionas una descripción textual detallada o un screenshot
2. **Generación**: el agente genera el código
3. **Ejecución**: tú lo ejecutas en el simulador
4. **Feedback visual**: envías un screenshot del resultado al agente
5. **Iteración**: el agente ajusta basándose en las diferencias

```markdown
# Paso 1: Descripción
Create a profile screen for a fitness app:
- Top: circular avatar (120x120), name below, "Edit Profile" button
- Stats row: 3 items (Workouts, Streak, Level) horizontal
- Settings list: 5 items with icons and chevron right
Use React Native with StyleSheet. Follow patterns in src/screens/.

# Paso 4: Feedback
The profile screen has these issues:
- Avatar not centered horizontally
- Stats row items overlap on iPhone SE
- Settings list doesn't scroll
Fix these issues.
```

Después de 2-3 iteraciones, el resultado suele estar suficientemente cercano al diseño.

## React Native: particularidades

**Fortalezas**: componentes funcionales con hooks, StyleSheet, React Navigation (stack, tab, drawer), integración con APIs nativas (cámara, geolocalización).

**Debilidades**: confunde defaults de flexbox mobile vs web (`flexDirection` es `column` por defecto en RN), puede generar código que funciona en iOS pero no en Android, no detecta re-renders innecesarios ni listas sin `FlatList`.

```markdown
Create a [screen/component] for a React Native app.
Requirements: functional component with TypeScript, StyleSheet
(not inline), FlatList for lists > 20 items, Platform.select()
for iOS/Android differences, SafeAreaView for screens.
```

## Flutter: particularidades

**Fortalezas**: widget composition natural, Material Design con buena fidelidad, `setState`/`Provider`/`Riverpod` bien cubiertos, Dart idiomático.

**Debilidades**: genera widget trees de 10+ niveles sin extraer, puede mezclar Cupertino y Material en el mismo proyecto, Bloc/Cubit patterns pueden salir incorrectos sin ejemplos, platform channels (Swift/Kotlin) con calidad baja.

```markdown
Create a [screen/widget] for a Flutter app.
Requirements: StatelessWidget unless state is needed, extract
sub-widgets if build() exceeds 50 lines, const constructors,
Material Design 3, responsive with LayoutBuilder/MediaQuery.
```

## Desafíos comunes en mobile

### Navegación

La navegación necesita más dirección que cualquier otro aspecto. Sé explícito sobre la estructura completa:

```markdown
The app uses React Navigation v6:
- AuthStack (Login, Register, ForgotPassword)
- MainStack (TabNavigator)
  - HomeTab (HomeScreen, DetailScreen)
  - ProfileTab (ProfileScreen, SettingsScreen)
Create navigation from DetailScreen to SettingsScreen.
Push onto current stack, don't switch tabs.
```

### State management

Sin dirección, el agente mezclará soluciones. Define reglas claras en CLAUDE.md: estado local con useState/setState, estado del servidor con React Query/Riverpod, estado global con Zustand/Provider.

### Performance: el punto ciego

El agente no puede medir frame rate, memory usage, bundle size ni cold start. Tu responsabilidad es perfilar regularmente y reportar con datos concretos:

```markdown
Task list drops to 20fps scrolling 100+ items. Using ScrollView
with map(). Convert to FlatList with keyExtractor, React.memo
renderItem, getItemLayout, windowSize={5}.
```

## Resumen

| Aspecto | Qué esperar del agente | Tu responsabilidad |
|---------|----------------------|-------------------|
| Generación de pantallas | Estructura correcta, layout aproximado | Verificar en simulador |
| Navegación | Funcional con dirección explícita | Definir estructura completa upfront |
| State management | Funcional pero inconsistente | Reglas claras en CLAUDE.md |
| Performance | No optimiza por defecto | Perfilar y reportar con datos |
| Cross-platform | Puede olvidar diferencias iOS/Android | Testar en ambas plataformas |
