import streamlit as st
import sympy as sp
import pandas as pd

st.set_page_config(page_title="SPNL - Metode Bisection", layout="centered")

st.title("Aplikasi Web Solusi SPNL")
st.subheader("Metode Bisection")

st.write("""
Metode Bisection digunakan untuk mencari akar persamaan nonlinear f(x)=0
dengan membagi interval [a, b] secara berulang hingga memenuhi toleransi.
""")

# Input pengguna
x = sp.symbols('x')
fungsi_input = st.text_input("Masukkan fungsi f(x):", "x**3 - x - 2")
a = st.number_input("Batas bawah (a):", value=1.0)
b = st.number_input("Batas atas (b):", value=2.0)
toleransi = st.number_input("Toleransi error:", value=0.0001, format="%.6f")
max_iter = st.number_input("Maksimum iterasi:", value=20, step=1)

if st.button("Hitung"):
    try:
        f = sp.sympify(fungsi_input)
        f_func = sp.lambdify(x, f)

        fa = f_func(a)
        fb = f_func(b)

        if fa * fb > 0:
            st.error("Syarat tidak terpenuhi: f(a) dan f(b) harus berlainan tanda!")
        else:
            hasil = []
            for i in range(1, max_iter + 1):
                c = (a + b) / 2
                fc = f_func(c)

                hasil.append([i, a, b, c, fc])

                if abs(fc) < toleransi:
                    break

                if fa * fc < 0:
                    b = c
                    fb = fc
                else:
                    a = c
                    fa = fc

            df = pd.DataFrame(
                hasil,
                columns=["Iterasi", "a", "b", "c (Akar)", "f(c)"]
            )

            st.success("Perhitungan selesai")
            st.write("### Tabel Iterasi")
            st.dataframe(df, use_container_width=True)

            st.write(f"### Akar Hampiran: **{c:.6f}**")

    except Exception as e:
        st.error(f"Terjadi kesalahan: {e}")
