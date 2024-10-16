import java.util.*;

// Clase Usuario
class Usuario {
    String nombre;
    int edad;
    String email;
    Map<Integer, String> respuestas;

    public Usuario(String nombre, int edad, String email) {
        this.nombre = nombre;
        this.edad = edad;
        this.email = email;
        this.respuestas = new HashMap<>();
    }

    public void responderPregunta(int preguntaId, String respuesta) {
        respuestas.put(preguntaId, respuesta);
    }

    public Map<Integer, String> getRespuestas() {
        return respuestas;
    }

    public String getNombre() {
        return nombre;
    }
}

// Clase Pregunta
class Pregunta {
    int id;
    String texto;

    public Pregunta(int id, String texto) {
        this.id = id;
        this.texto = texto;
    }

    public String getTexto() {
        return texto;
    }

    public int getId() {
        return id;
    }
}

// Clase para manejar el sistema
class Sistema {
    List<Usuario> usuarios = new ArrayList<>();
    List<Pregunta> preguntas = new ArrayList<>();
    Map<String, List<String>> conversaciones = new HashMap<>();
    Map<String, Integer> coincidencias = new HashMap<>();

    // Registro de usuarios
    public void registrarUsuario(Usuario usuario) {
        usuarios.add(usuario);
    }

    // Agregar preguntas al sistema
    public void agregarPregunta(Pregunta pregunta) {
        preguntas.add(pregunta);
    }

    // Mostrar preguntas disponibles
    public void mostrarPreguntas() {
        for (Pregunta pregunta : preguntas) {
            System.out.println("Pregunta ID " + pregunta.getId() + ": " + pregunta.getTexto());
        }
    }

    // Buscar coincidencias
    public void buscarCoincidencias(Usuario user1, Usuario user2) {
        int totalCoincidencias = 0;
        for (Map.Entry<Integer, String> entry : user1.getRespuestas().entrySet()) {
            if (user2.getRespuestas().get(entry.getKey()) != null && user2.getRespuestas().get(entry.getKey()).equals(entry.getValue())) {
                totalCoincidencias++;
            }
        }

        if (totalCoincidencias > 0) {
            System.out.println("¡Coincidiste con " + user2.getNombre() + "! Respuestas similares: " + totalCoincidencias);
            coincidencias.put(user1.getNombre() + "-" + user2.getNombre(), totalCoincidencias);
        } else {
            System.out.println("No hubo coincidencias con " + user2.getNombre() + ".");
        }
    }

    // Guardar conversación entre usuarios
    public void guardarConversacion(String user1, String user2, String mensaje) {
        String key = user1 + "-" + user2;
        if (!conversaciones.containsKey(key)) {
            conversaciones.put(key, new ArrayList<>());
        }
        conversaciones.get(key).add(mensaje);
    }

    // Mostrar conversación entre usuarios
    public void mostrarConversacion(String user1, String user2) {
        String key = user1 + "-" + user2;
        if (conversaciones.containsKey(key)) {
            System.out.println("Conversación entre " + user1 + " y " + user2 + ":");
            for (String mensaje : conversaciones.get(key)) {
                System.out.println(mensaje);
            }
        } else {
            System.out.println("No hay conversaciones entre estos usuarios.");
        }
    }
}

// Main para ejecutar el sistema con interacción de teclado
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Sistema sistema = new Sistema();

        // Registrar usuarios
        System.out.println("Registro de Usuario 1:");
        System.out.print("Nombre: ");
        String nombre1 = scanner.nextLine();
        System.out.print("Edad: ");
        int edad1 = scanner.nextInt();
        scanner.nextLine();  // Consumir el salto de línea
        System.out.print("Email: ");
        String email1 = scanner.nextLine();
        Usuario user1 = new Usuario(nombre1, edad1, email1);
        sistema.registrarUsuario(user1);

        System.out.println("Registro de Usuario 2:");
        System.out.print("Nombre: ");
        String nombre2 = scanner.nextLine();
        System.out.print("Edad: ");
        int edad2 = scanner.nextInt();
        scanner.nextLine();  // Consumir el salto de línea
        System.out.print("Email: ");
        String email2 = scanner.nextLine();
        Usuario user2 = new Usuario(nombre2, edad2, email2);
        sistema.registrarUsuario(user2);

        // Agregar preguntas
        sistema.agregarPregunta(new Pregunta(1, "¿Cuál es tu color favorito?"));
        sistema.agregarPregunta(new Pregunta(2, "¿Cuál es tu número favorito?"));

        // Mostrar preguntas y pedir respuestas
        System.out.println("\nUsuario 1 responde:");
        sistema.mostrarPreguntas();
        for (Pregunta pregunta : sistema.preguntas) {
            System.out.print("Respuesta a la pregunta '" + pregunta.getTexto() + "': ");
            String respuesta = scanner.nextLine();
            user1.responderPregunta(pregunta.getId(), respuesta);
        }

        System.out.println("\nUsuario 2 responde:");
        sistema.mostrarPreguntas();
        for (Pregunta pregunta : sistema.preguntas) {
            System.out.print("Respuesta a la pregunta '" + pregunta.getTexto() + "': ");
            String respuesta = scanner.nextLine();
            user2.responderPregunta(pregunta.getId(), respuesta);
        }

        // Buscar coincidencias
        sistema.buscarCoincidencias(user1, user2);

        // Chat básico
        String continuar = "s";
        while (continuar.equalsIgnoreCase("s")) {
            System.out.println("\n" + user1.getNombre() + " escribe un mensaje a " + user2.getNombre() + ": ");
            String mensaje1 = scanner.nextLine();
            sistema.guardarConversacion(user1.getNombre(), user2.getNombre(), user1.getNombre() + ": " + mensaje1);

            System.out.println("\n" + user2.getNombre() + " responde a " + user1.getNombre() + ": ");
            String mensaje2 = scanner.nextLine();
            sistema.guardarConversacion(user2.getNombre(), user1.getNombre(), user2.getNombre() + ": " + mensaje2);

            // Mostrar conversación
            sistema.mostrarConversacion(user1.getNombre(), user2.getNombre());

            System.out.print("\n¿Desean seguir conversando? (s/n): ");
            continuar = scanner.nextLine();
        }

        scanner.close();
    }
}
