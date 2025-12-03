# 🌍 Migración: Rick & Morty → Rest Countries API

Pasos exactos para cambiar de Rick & Morty API a Rest Countries API.

---

## 📝 Cambios Necesarios

### 1️⃣ `src/pages/Home.jsx`

**❌ ELIMINAR:**
```jsx
const response = await fetch('https://rickandmortyapi.com/api/character');
const data = await response.json();
setItems(data.results.slice(0, 6));
```

**✅ REEMPLAZAR POR:**
```jsx
const response = await fetch('https://restcountries.com/v3.1/all');
const data = await response.json();
// ⚠️ IMPORTANTE: Verificar que data es un array
if (Array.isArray(data)) {
  setItems(data.slice(0, 6));
}
```

**✅ CAMBIAR TAMBIÉN EL TÍTULO:**
```jsx
<h1 className="display-4">World Countries</h1>
<p className="lead">Exploring countries with Rest Countries API</p>
```

---

### 2️⃣ `src/pages/Entities.jsx`

**❌ ELIMINAR TODO EL CÓDIGO ACTUAL**

**✅ REEMPLAZAR POR:**
```jsx
import { useState, useEffect } from 'react';
import { useStore } from '../store/store';
import CardList from '../components/CardList';

const Entities = () => {
  const { items, setItems } = useStore();
  const [allCountries, setAllCountries] = useState([]);
  const [page, setPage] = useState(1);
  const itemsPerPage = 12;

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('https://restcountries.com/v3.1/all');
        const data = await response.json();
        // ⚠️ IMPORTANTE: Verificar que es un array
        if (Array.isArray(data)) {
          setAllCountries(data);
        }
      } catch (error) {
        console.error('Error fetching countries:', error);
      }
    };
    fetchData();
  }, []);

  useEffect(() => {
    // ⚠️ IMPORTANTE: Verificar que allCountries tiene datos
    if (allCountries.length > 0) {
      const startIndex = (page - 1) * itemsPerPage;
      const endIndex = startIndex + itemsPerPage;
      setItems(allCountries.slice(startIndex, endIndex));
    }
  }, [page, allCountries, setItems]);

  const totalPages = Math.ceil(allCountries.length / itemsPerPage);

  return (
    <div className="container py-5">
      <h2 className="mb-4">All Countries</h2>
      <CardList items={items} />

      <div className="d-flex justify-content-center gap-2 mt-4">
        <button 
          className="btn btn-primary" 
          onClick={() => setPage(page - 1)} 
          disabled={page === 1}
        >
          Previous
        </button>
        <span className="btn btn-outline-secondary disabled">
          Page {page} of {totalPages}
        </span>
        <button 
          className="btn btn-primary" 
          onClick={() => setPage(page + 1)}
          disabled={page === totalPages}
        >
          Next
        </button>
      </div>
    </div>
  );
};

export default Entities;
```

---

### 3️⃣ `src/components/Card.jsx`

**❌ ELIMINAR TODO EL CÓDIGO ACTUAL**

**✅ REEMPLAZAR POR:**
```jsx
const Card = ({ item }) => {
  return (
    <div className="col">
      <div className="card h-100">
        <img 
          src={item.flags?.png || 'https://via.placeholder.com/300'} 
          className="card-img-top" 
          alt={item.name?.common || 'Country flag'}
          style={{height: '200px', objectFit: 'cover'}}
        />
        <div className="card-body">
          <h5 className="card-title">{item.name?.common || 'Unknown'}</h5>
          <p className="card-text mb-1">
            <strong>🏛️ Capital:</strong> {item.capital?.[0] || 'N/A'}
          </p>
          <p className="card-text mb-1">
            <strong>🌎 Region:</strong> {item.region || 'N/A'}
          </p>
          <p className="card-text mb-0">
            <strong>👥 Population:</strong> {item.population?.toLocaleString() || 'N/A'}
          </p>
        </div>
      </div>
    </div>
  );
};

export default Card;
```

---

## 🎯 Resumen de Cambios

| Archivo | Acción |
|---------|--------|
| `Home.jsx` | Cambiar URL y usar `data.slice(0, 6)` |
| `Entities.jsx` | Reemplazar todo (lógica de paginación local) |
| `Card.jsx` | Reemplazar todo (mostrar bandera, capital, región, población) |

---

## ✅ Verificación

Después de los cambios, deberías ver:

- ✅ Banderas de países en lugar de personajes
- ✅ Información: nombre, capital, región, población
- ✅ Paginación funcionando (12 países por página)
- ✅ 6 países en Home
- ✅ Todos los países en Entities

---

## 🚀 Comandos Finales

```bash
npm run dev
```

¡Listo! Tu app ahora consume Rest Countries API 🌍
