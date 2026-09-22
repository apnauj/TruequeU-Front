# TruequeU — cliente web

Interfaz de la plataforma de trueque entre estudiantes: publicar objetos, buscarlos,
guardarlos como favoritos, conversar con el dueño y cerrar el intercambio. Incluye un panel
de moderación para administradores.

> **Nota.** Este es un fork de [DuqueJR/TruequeU](https://github.com/DuqueJR/TruequeU),
> desarrollado en equipo para el curso de Ingeniería Web en la Universidad EIA. Lo conservo
> aquí como parte de mi portafolio; el crédito del trabajo es compartido.

La API que consume está en **[apnauj/TruequeU](https://github.com/apnauj/TruequeU)**
(.NET 10 + SQL Server).

**Stack** · React 19 · TypeScript · Vite · Tailwind CSS 4 · React Router 7 · Zustand

---

## Cómo correrlo

Necesitas Node 20 o superior y la API corriendo en `http://localhost:5000`.

```bash
git clone https://github.com/apnauj/TruequeU-Front.git
cd TruequeU-Front

npm install
npm run dev        # http://localhost:5173
npm run build      # compila TypeScript y genera dist/
npm run lint
```

El puerto 5173 no es arbitrario: es uno de los dos orígenes que la política CORS de la API
permite con credenciales.

---

## Estructura

```
src/api/         client.ts — fetch con cookies; mappers.ts — DTO de la API → tipo de UI
src/services/    auth.service.ts, listing.service.ts — una función por operación
src/store/       useStore.ts — estado global con Zustand
src/pages/       Una por ruta: login, sign-up, home, listings, listing-details,
                 create-listing, chat, favorites, profile, admin-dashboard, not-found
src/components/  AuthGuard, ThemeProvider, Navbar, ListingCard, ListingList, ReportForm
```

Las páginas no llaman a `fetch` directamente: pasan por `services/`, que a su vez usa el
cliente de `api/`. Así el manejo de errores y el envío de credenciales viven en un solo
lugar.

### Autenticación

La API no devuelve el token en el cuerpo, sino en una cookie `HttpOnly` que el navegador
guarda y reenvía solo. El cliente nunca lee ni almacena el JWT — de hecho, no puede — así
que todas las peticiones van con `credentials: "include"` y el estado de sesión se infiere
del endpoint `/me`.

`AuthGuard` envuelve las rutas privadas y redirige a login cuando esa consulta falla.

### Capa de mapeo

`api/mappers.ts` traduce los DTO de la API a los tipos que usa la interfaz. Existe para que
un cambio de nombre de campo en el backend se arregle en un archivo y no en quince
componentes.

## Limitaciones conocidas

- No hay pruebas automatizadas.
- La URL de la API está fija en el cliente; debería venir de una variable de entorno de Vite.
- No hay estados de carga ni de error unificados: cada página los maneja por su cuenta.
