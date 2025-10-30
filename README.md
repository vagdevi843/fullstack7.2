# fullstack7.2
// App.js
import React from "react";
import { Provider, useDispatch, useSelector } from "react-redux";
import { configureStore, createSlice } from "@reduxjs/toolkit";

// =============================
// Redux Slice (State + Reducers)
// =============================
const cartSlice = createSlice({
  name: "cart",
  initialState: {
    cartItems: [],
    totalQuantity: 0,
    totalAmount: 0,
  },
  reducers: {
    addToCart: (state, action) => {
      const item = state.cartItems.find(i => i.id === action.payload.id);
      if (item) {
        item.quantity += 1;
      } else {
        state.cartItems.push({ ...action.payload, quantity: 1 });
      }
      state.totalQuantity += 1;
      state.totalAmount += action.payload.price;
    },
    removeFromCart: (state, action) => {
      const item = state.cartItems.find(i => i.id === action.payload);
      if (item) {
        state.totalQuantity -= item.quantity;
        state.totalAmount -= item.price * item.quantity;
        state.cartItems = state.cartItems.filter(i => i.id !== action.payload);
      }
    },
    clearCart: (state) => {
      state.cartItems = [];
      state.totalQuantity = 0;
      state.totalAmount = 0;
    },
  },
});

const { addToCart, removeFromCart, clearCart } = cartSlice.actions;

// =============================
// Redux Store
// =============================
const store = configureStore({
  reducer: {
    cart: cartSlice.reducer,
  },
});

// =============================
// Product List Component
// =============================
const ProductList = () => {
  const dispatch = useDispatch();

  const products = [
    { id: 1, name: "Laptop", price: 800 },
    { id: 2, name: "Smartphone", price: 500 },
    { id: 3, name: "Headphones", price: 120 },
    { id: 4, name: "Keyboard", price: 80 },
  ];

  return (
    <div style={styles.section}>
      <h2>🛍️ Available Products</h2>
      <div style={styles.grid}>
        {products.map((p) => (
          <div key={p.id} style={styles.card}>
            <h3>{p.name}</h3>
            <p>${p.price}</p>
            <button style={styles.button} onClick={() => dispatch(addToCart(p))}>
              Add to Cart
            </button>
          </div>
        ))}
      </div>
    </div>
  );
};

// =============================
// Cart Component
// =============================
const Cart = () => {
  const { cartItems, totalQuantity, totalAmount } = useSelector((state) => state.cart);
  const dispatch = useDispatch();

  return (
    <div style={styles.section}>
      <h2>🛒 Shopping Cart</h2>

      {cartItems.length === 0 ? (
        <p>Your cart is empty!</p>
      ) : (
        <>
          <ul style={{ listStyleType: "none", padding: 0 }}>
            {cartItems.map((item) => (
              <li key={item.id} style={styles.listItem}>
                <span>
                  {item.name} (x{item.quantity})
                </span>
                <span>
                  ${item.price * item.quantity}
                  <button
                    style={styles.removeButton}
                    onClick={() => dispatch(removeFromCart(item.id))}
                  >
                    ❌
                  </button>
                </span>
              </li>
            ))}
          </ul>

          <div style={styles.summary}>
            <p>Total Items: {totalQuantity}</p>
            <p>Total Amount: ${totalAmount.toFixed(2)}</p>
            <button style={styles.clearButton} onClick={() => dispatch(clearCart())}>
              Clear Cart
            </button>
          </div>
        </>
      )}
    </div>
  );
};

// =============================
// App Component
// =============================
function ShoppingCartApp() {
  return (
    <div style={styles.container}>
      <h1 style={{ textAlign: "center" }}>🧺 Redux Toolkit Shopping Cart</h1>
      <ProductList />
      <Cart />
    </div>
  );
}

// =============================
// Styles (Inline)
// =============================
const styles = {
  container: {
    maxWidth: "800px",
    margin: "0 auto",
    padding: "20px",
    fontFamily: "Arial, sans-serif",
  },
  section: {
    marginTop: "30px",
    padding: "20px",
    border: "1px solid #ccc",
    borderRadius: "10px",
    backgroundColor: "#fafafa",
  },
  grid: {
    display: "grid",
    gridTemplateColumns: "repeat(auto-fit, minmax(150px, 1fr))",
    gap: "15px",
  },
  card: {
    border: "1px solid #ddd",
    borderRadius: "10px",
    padding: "10px",
    textAlign: "center",
    boxShadow: "0 1px 4px rgba(0,0,0,0.1)",
  },
  button: {
    backgroundColor: "#007bff",
    color: "white",
    border: "none",
    padding: "6px 10px",
    borderRadius: "6px",
    cursor: "pointer",
  },
  removeButton: {
    backgroundColor: "transparent",
    border: "none",
    cursor: "pointer",
    fontSize: "16px",
    marginLeft: "8px",
  },
  clearButton: {
    backgroundColor: "#e74c3c",
    color: "white",
    border: "none",
    padding: "8px 12px",
    borderRadius: "6px",
    cursor: "pointer",
  },
  listItem: {
    display: "flex",
    justifyContent: "space-between",
    borderBottom: "1px solid #ddd",
    padding: "6px 0",
  },
  summary: {
    marginTop: "10px",
  },
};

// =============================
// Root Export
// =============================
export default function App() {
  return (
    <Provider store={store}>
      <ShoppingCartApp />
    </Provider>
  );
}
