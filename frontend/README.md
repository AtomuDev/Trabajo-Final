# ElectroFit — Frontend

SPA desarrollada en **React + TypeScript**.

## Estado

Aún no iniciado. La implementación comienza tras la validación del modelo de base de datos y los módulos en la 2.ª Entrega (27/09).

## Estructura prevista

```
frontend/
├── src/
│   ├── pages/            → páginas públicas y privadas (ver mapa de navegación)
│   ├── components/       → componentes reutilizables
│   ├── services/          → llamadas a la API (axios/fetch)
│   ├── context/           → contexto de autenticación/rol
│   ├── types/              → tipos TypeScript compartidos
│   └── routes/            → configuración de React Router
├── public/
├── package.json
└── tsconfig.json
```

## Cómo correrlo (a completar cuando el proyecto esté inicializado)

```bash
npm install
npm run dev
```

Ver el mapa de navegación completo en [`../docs/03-modulos.md`](../docs/03-modulos.md).
