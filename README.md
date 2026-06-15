tarea de phyton 
ejercicio 1
import numpy as np
import matplotlib.pyplot as plt

# Parámetro
A = 1  # Amplitud (puedes cambiarla)

# Intervalo de tiempo
t = np.linspace(0, 6, 1000)

# Función de posición
x = A * np.exp(-t/3) * np.cos(2*np.pi*t + np.pi/4)

# Primera derivada (velocidad)
v = A * np.exp(-t/3) * (
    -(1/3)*np.cos(2*np.pi*t + np.pi/4)
    - 2*np.pi*np.sin(2*np.pi*t + np.pi/4)
)

# Segunda derivada (aceleración)
a = A * np.exp(-t/3) * (
    (1/9 - 4*np.pi**2)*np.cos(2*np.pi*t + np.pi/4)
    + (4*np.pi/3)*np.sin(2*np.pi*t + np.pi/4)
)

# Gráficas
plt.figure(figsize=(10, 8))

plt.subplot(3, 1, 1)
plt.plot(t, x)
plt.title('Posición x(t)')
plt.ylabel('x (m)')
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(t, v)
plt.title('Velocidad x\'(t)')
plt.ylabel('v (m/s)')
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(t, a)
plt.title('Aceleración x\'\'(t)')
plt.xlabel('Tiempo (s)')
plt.ylabel('a (m/s²)')
plt.grid(True)

plt.tight_layout()
plt.show()

Ejercicio 2
import sympy as sp
import numpy as np
import matplotlib.pyplot as plt

# Variables simbólicas
t = sp.symbols('t')
A = 1  # Amplitud

# Función de posición
x = A * sp.exp(-t/3) * sp.cos(2*sp.pi*t + sp.pi/4)

# Primera derivada (velocidad)
v = sp.diff(x, t)

# Segunda derivada (aceleración)
a = sp.diff(v, t)

# Mostrar derivadas en consola
print("Posición x(t):")
sp.pprint(x)

print("\nVelocidad v(t) = dx/dt:")
sp.pprint(v)

print("\nAceleración a(t) = d²x/dt²:")
sp.pprint(a)

# Convertir expresiones simbólicas a funciones numéricas
x_num = sp.lambdify(t, x, 'numpy')
v_num = sp.lambdify(t, v, 'numpy')
a_num = sp.lambdify(t, a, 'numpy')

# Intervalo de tiempo
t_val = np.linspace(0, 6, 1000)

# Evaluación numérica
x_val = x_num(t_val)
v_val = v_num(t_val)
a_val = a_num(t_val)

# Crear figura con subplots
plt.figure(figsize=(10, 8))

# Gráfica de posición
plt.subplot(3, 1, 1)
plt.plot(t_val, x_val, 'r', linewidth=2)
plt.title('Posición x(t)')
plt.xlabel('Tiempo (s)')
plt.ylabel('Posición (m)')
plt.xlim(0, 6)
plt.grid(True)

# Gráfica de velocidad
plt.subplot(3, 1, 2)
plt.plot(t_val, v_val, 'b', linewidth=2)
plt.title('Velocidad v(t)')
plt.xlabel('Tiempo (s)')
plt.ylabel('Velocidad (m/s)')
plt.xlim(0, 6)
plt.grid(True)

# Gráfica de aceleración
plt.subplot(3, 1, 3)
plt.plot(t_val, a_val, 'g', linewidth=2)
plt.title('Aceleración a(t)')
plt.xlabel('Tiempo (s)')
plt.ylabel('Aceleración (m/s²)')
plt.xlim(0, 6)
plt.grid(True)

# Ajustar espacios entre gráficas
plt.tight_layout()

# Mostrar figura
plt.show()


Ejercicio 3 
import numpy as np
import sympy as sp
import matplotlib.pyplot as plt
from matplotlib.widgets import Slider
from scipy.integrate import simpson

# =====================================================
# DEFINICIÓN DE LA FUNCIÓN Y SUS DERIVADAS
# =====================================================

t = sp.symbols('t')
A = 1

x = A * sp.exp(-t/3) * sp.cos(2*sp.pi*t + sp.pi/4)

# Derivadas simbólicas
v = sp.diff(x, t)
a = sp.diff(v, t)

# Conversión a funciones numéricas
x_fun = sp.lambdify(t, x, 'numpy')
v_fun = sp.lambdify(t, v, 'numpy')
a_fun = sp.lambdify(t, a, 'numpy')

# =====================================================
# INTERVALO DE TIEMPO
# =====================================================

t_total = np.linspace(0, 6, 2000)

v_total = v_fun(t_total)
a_total = a_fun(t_total)

# =====================================================
# FIGURA
# =====================================================

fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(10, 8))
plt.subplots_adjust(bottom=0.25)

# Valor inicial del deslizador
t0 = 3

# Datos iniciales
mask = t_total <= t0

# =====================================================
# GRÁFICA DE VELOCIDAD
# =====================================================

line_v, = ax1.plot(t_total, v_total,
                   color='blue',
                   label='v(t)')

fill_v = ax1.fill_between(
    t_total[mask],
    v_total[mask],
    alpha=0.3
)

ax1.set_title('Integral de la velocidad')
ax1.set_xlabel('Tiempo (s)')
ax1.set_ylabel('v(t) [m/s]')
ax1.grid(True)

# =====================================================
# GRÁFICA DE ACELERACIÓN
# =====================================================

line_a, = ax2.plot(t_total, a_total,
                   color='green',
                   label='a(t)')

fill_a = ax2.fill_between(
    t_total[mask],
    a_total[mask],
    alpha=0.3
)

ax2.set_title('Integral de la aceleración')
ax2.set_xlabel('Tiempo (s)')
ax2.set_ylabel('a(t) [m/s²]')
ax2.grid(True)

# =====================================================
# CÁLCULO INICIAL DE INTEGRALES
# =====================================================

t_sel = t_total[mask]

trap_v = np.trapz(v_total[mask], t_sel)
simp_v = simpson(v_total[mask], t_sel)

trap_a = np.trapz(a_total[mask], t_sel)
simp_a = simpson(a_total[mask], t_sel)

texto = fig.text(
    0.05,
    0.02,
    f'Velocidad → Trapecio = {trap_v:.6f} | Simpson = {simp_v:.6f}\n'
    f'Aceleración → Trapecio = {trap_a:.6f} | Simpson = {simp_a:.6f}',
    fontsize=10
)

# =====================================================
# SLIDER
# =====================================================

slider_ax = plt.axes([0.15, 0.1, 0.7, 0.03])

slider = Slider(
    ax=slider_ax,
    label='Tiempo final t (s)',
    valmin=0.1,
    valmax=6.0,
    valinit=t0,
    valstep=0.1
)

# =====================================================
# FUNCIÓN DE ACTUALIZACIÓN
# =====================================================

def actualizar(val):

    global fill_v, fill_a

    tiempo = slider.val

    mask = t_total <= tiempo

    t_sel = t_total[mask]

    # Eliminar áreas anteriores
    fill_v.remove()
    fill_a.remove()

    # Dibujar nuevas áreas
    fill_v = ax1.fill_between(
        t_total[mask],
        v_total[mask],
        alpha=0.3
    )

    fill_a = ax2.fill_between(
        t_total[mask],
        a_total[mask],
        alpha=0.3
    )

    # Integración por Trapecio
    trap_v = np.trapz(v_total[mask], t_sel)
    trap_a = np.trapz(a_total[mask], t_sel)

    # Integración por Simpson
    simp_v = simpson(v_total[mask], t_sel)
    simp_a = simpson(a_total[mask], t_sel)

    texto.set_text(
        f'Velocidad → Trapecio = {trap_v:.6f} | Simpson = {simp_v:.6f}\n'
        f'Aceleración → Trapecio = {trap_a:.6f} | Simpson = {simp_a:.6f}'
    )

    fig.canvas.draw_idle()

slider.on_changed(actualizar)

plt.show()
