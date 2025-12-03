# 🌍 Solución: Error 400 en Rest Countries API

## ⚠️ Problema Detectado

La API `https://restcountries.com/v3.1/all` está devolviendo error 400 (Bad Request).

---

## ✅ SOLUCIÓN: Usar JSONPlaceholder + Flags API

Vamos a usar una combinación más confiable:

---

## 📝 Cambios Necesarios

### 1️⃣ `src/pages/Home.jsx`

**REEMPLAZA TODO EL `useEffect` POR:**

```jsx
useEffect(() => {
  const fetchData = async () => {
    try {
      const response = await fetch('https://restcountries.com/v3.1/region/europe');
      const data = await response.json();
      if (Array.isArray(data)) {
        setItems(data.slice(0, 6));
      }
    } catch (error) {
      console.error('Error:', error);
      setItems([]);
    }
  };
  if (items.length === 0) {
    fetchData();
  }
}, []);
```

---

### 2️⃣ `src/pages/Entities.jsx`

**REEMPLAZA TODO EL ARCHIVO POR:**

```jsx
import { useState, useEffect } from 'react';
import { useStore } from '../store/store';
import CardList from '../components/CardList';

const Entities = () => {
  const { items, setItems } = useStore();
  const [allCountries, setAllCountries] = useState([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(true);
  const itemsPerPage = 12;

  useEffect(() => {
    const fetchData = async () => {
      setLoading(true);
      try {
        const response = await fetch('https://restcountries.com/v3.1/region/europe');
        const data = await response.json();
        
        if (Array.isArray(data)) {
          setAllCountries(data);
        }
      } catch (error) {
        console.error('Error fetching countries:', error);
        setAllCountries([]);
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, []);

  useEffect(() => {
    if (allCountries.length > 0) {
      const startIndex = (page - 1) * itemsPerPage;
      const endIndex = startIndex + itemsPerPage;
      setItems(allCountries.slice(startIndex, endIndex));
    }
  }, [page, allCountries, setItems]);

  const totalPages = Math.ceil(allCountries.length / itemsPerPage);

  if (loading) {
    return (
      <div className="container py-5 text-center">
        <div className="spinner-border" role="status">
          <span className="visually-hidden">Loading...</span>
        </div>
      </div>
    );
  }

  return (
    <div className="container py-5">
      <h2 className="mb-4">European Countries</h2>
      
      {items.length === 0 ? (
        <p>No countries found</p>
      ) : (
        <CardList items={items} />
      )}

      {totalPages > 0 && (
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
            disabled={page >= totalPages}
          >
            Next
          </button>
        </div>
      )}
    </div>
  );
};

export default Entities;
```

---

### 3️⃣ `src/components/Card.jsx`

**MANTÉN ESTE CÓDIGO (NO CAMBIAR):**

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

## 🔧 ¿Por Qué Este Cambio Funciona?

1. ✅ Usamos `/v3.1/region/europe` en lugar de `/v3.1/all` (más confiable)
2. ✅ Agregamos manejo de errores con `try-catch`
3. ✅ Verificamos que `data` sea un array antes de usar `.slice()`
4. ✅ Agregamos estado de "loading"
5. ✅ Protegemos contra páginas inválidas

---

## 🎯 Alternativa 2: API de Países Americas

Si también falla Europa, cambia la URL por:

```jsx
const response = await fetch('https://restcountries.com/v3.1/region/americas');
```

---

## 🚀 Verifica que funcione:

1. Guarda todos los archivos
2. Refresca el navegador (Ctrl + R o Cmd + R)
3. Abre la consola (F12) para ver si hay errores
4. Deberías ver países europeos cargando

---

## 📌 Si TODAVÍA hay error 400:

Es posible que tu red o ISP esté bloqueando la API. En ese caso:

### Opción B: Usar API alternativa (Countries Now)

**Cambia la URL en ambos archivos por:**

```jsx
const response = await fetch('https://countriesnow.space/api/v0.1/countries/flag/images');
```

Y ajusta el Card.jsx para usar:
- `data.data` en lugar de `data`
- `item.flag` en lugar de `item.flags.png`
- `item.name` para el nombre
