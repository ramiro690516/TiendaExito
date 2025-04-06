package com.mycompany.tiendaexitoapp;

import javax.swing.*;
import java.awt.*;
import java.util.ArrayList;

public class TiendaExitoApp extends JFrame {
    private final JButton btnCalificar;
    private Pedido pedido;

    public TiendaExitoApp() {
        setTitle("Tienda Éxito - Inicio");
        setSize(400, 400);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        pedido = new Pedido();

        JButton btnBebidas = new JButton("Bebidas");
        JButton btnJugos = new JButton("Jugos");
        JButton btnSnacks = new JButton("Snacks");
        JButton btnAseo = new JButton("Aseo Personal");
        btnCalificar = new JButton("Calificar App");
        btnCalificar.setEnabled(false);

        btnBebidas.addActionListener(e -> new SubcategoriaVentana("Bebidas", new String[]{"Gaseosa", "Agua", "Cerveza"}, this, pedido));
        btnJugos.addActionListener(e -> new SubcategoriaVentana("Jugos", new String[]{"Natural", "En caja"}, this, pedido));
        btnSnacks.addActionListener(e -> new SubcategoriaVentana("Snacks", new String[]{"Papas", "Chocolates"}, this, pedido));
        btnAseo.addActionListener(e -> new SubcategoriaVentana("Aseo Personal", new String[]{"Jabón", "Shampoo"}, this, pedido));
        btnCalificar.addActionListener(e -> new CalificarVentana(this, pedido));

        JPanel panel = new JPanel(new GridLayout(5, 1, 10, 10));
        panel.setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));
        panel.add(btnBebidas);
        panel.add(btnJugos);
        panel.add(btnSnacks);
        panel.add(btnAseo);
        panel.add(btnCalificar);

        add(panel);
        setVisible(true);
    }

    public void activarBotonCalificar() {
        btnCalificar.setEnabled(true);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(TiendaExitoApp::new);
    }
}

// Clase para manejar el pedido
class Pedido {
    private final java.util.List<String> productos = new ArrayList<>();

    public void agregarProducto(String producto) {
        productos.add(producto);
    }

    public boolean tieneProductos() {
        return !productos.isEmpty();
    }

    public String resumen() {
        StringBuilder resumen = new StringBuilder("Tu pedido:\n");
        for (String p : productos) {
            resumen.append("- ").append(p).append("\n");
        }
        return resumen.toString();
    }
}

// Ventana para mostrar subcategorías
class SubcategoriaVentana extends JFrame {
    public SubcategoriaVentana(String categoria, String[] subcategorias, TiendaExitoApp inicio, Pedido pedido) {
        setTitle("Categoría: " + categoria);
        setSize(300, 250);
        setLocationRelativeTo(null);

        JPanel panel = new JPanel(new GridLayout(subcategorias.length + 1, 1, 5, 5));
        panel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        for (String sub : subcategorias) {
            JButton btnSub = new JButton(sub);
            btnSub.addActionListener(e -> {
                if (sub.equals("Agua")) {
                    new SubcategoriaVentana("Agua", new String[]{"H2O", "Brisa", "Cristal"}, inicio, pedido);
                } else {
                    pedido.agregarProducto(sub);
                    JOptionPane.showMessageDialog(this, sub + " agregado al pedido.");
                    if (pedido.tieneProductos()) {
                        inicio.activarBotonCalificar();
                    }
                }
            });
            panel.add(btnSub);
        }

        JButton btnVolver = new JButton("Volver al Inicio");
        btnVolver.addActionListener(e -> {
            inicio.setVisible(true);
            dispose();
        });

        panel.add(btnVolver);
        add(panel);
        setVisible(true);
        inicio.setVisible(false);
    }
}

// Ventana de calificación con resumen
class CalificarVentana extends JFrame {
    public CalificarVentana(TiendaExitoApp inicio, Pedido pedido) {
        setTitle("Calificar la App");
        setSize(300, 300);
        setLocationRelativeTo(null);

        JTextArea areaResumen = new JTextArea(pedido.resumen());
        areaResumen.setEditable(false);
        areaResumen.setBorder(BorderFactory.createTitledBorder("Resumen del Pedido"));

        JLabel lbl = new JLabel("¿Cuántas estrellas le das a la app?", SwingConstants.CENTER);
        JPanel estrellasPanel = new JPanel();
        JButton[] estrellas = new JButton[5];

        for (int i = 0; i < 5; i++) {
            int rating = i + 1;
            estrellas[i] = new JButton("★");
            estrellas[i].setFont(new Font("SansSerif", Font.PLAIN, 20));
            estrellas[i].addActionListener(e ->
                JOptionPane.showMessageDialog(this, "Gracias por calificar con " + rating + " estrella(s)!")
            );
            estrellasPanel.add(estrellas[i]);
        }

        JButton btnVolver = new JButton("Volver al Inicio");
        btnVolver.addActionListener(e -> {
            inicio.setVisible(true);
            dispose();
        });

        setLayout(new BorderLayout());
        add(areaResumen, BorderLayout.NORTH);
        add(lbl, BorderLayout.CENTER);
        add(estrellasPanel, BorderLayout.SOUTH);
        add(btnVolver, BorderLayout.PAGE_END);

        setVisible(true);
        inicio.setVisible(false);
    }
}


