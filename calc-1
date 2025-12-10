import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'dart:math' show pow;

void main() => runApp(const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: HipotecaFacilScreen(),
    ));

class HipotecaFacilScreen extends StatefulWidget {
  const HipotecaFacilScreen({Key? key}) : super(key: key);
  @override
  State<HipotecaFacilScreen> createState() => _HipotecaFacilScreenState();
}

class _HipotecaFacilScreenState extends State<HipotecaFacilScreen> {
  final _formKey = GlobalKey<FormState>();

  // Controladores
  final _montoController = TextEditingController(text: "350000000");
  final _plazoController = TextEditingController(text: "25");
  final _tasaController = TextEditingController(text: "4.15");

  String _tipoCuota = "Francesa"; // Francesa o Alemana
  bool _calculado = false;

  // Resultados
  double cuotaMensual = 0;
  double totalIntereses = 0;
  double totalAPagar = 0;

  void _calcular() {
    if (!_formKey.currentState!.validate()) return;

    final monto = double.parse(_montoController.text.replaceAll(".", ""));
    final anos = int.parse(_plazoController.text);
    final tasaAnual = double.parse(_tasaController.text) / 100;
    final meses = anos * 12;
    final tasaMensual = tasaAnual / 12;

    setState(() {
      if (_tipoCuota == "Francesa") {
        // Sistema francés (cuota fija)
        cuotaMensual = monto *
            (tasaMensual * pow(1 + tasaMensual, meses)) /
            (pow(1 + tasaMensual, meses) - 1);
      } else {
        // Sistema alemán (amortización capital constante)
        final capitalMensual = monto / meses;
        cuotaMensual = capitalMensual + (monto - capitalMensual) * tasaMensual;
      }

      totalAPagar = cuotaMensual * meses;
      totalIntereses = totalAPagar - monto;
      _calculado = true;
    });

    HapticFeedback.mediumImpact();
  }

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);
    final isDark = theme.brightness == Brightness.dark;

    return Scaffold(
      appBar: AppBar(
        title: const Text("Hipoteca Fácil"),
        backgroundColor: const Color(0xFF1E40AF),
        foregroundColor: Colors.white,
        actions: [
          IconButton(
            icon: const Icon(Icons.install_mobile),
            onPressed: () {
              ScaffoldMessenger.of(context).showSnackBar(
                const SnackBar(content: Text("PWA instalable desde el navegador")),
              );
            },
          ),
        ],
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              // === INPUTS ===
              _buildTextField(
                controller: _montoController,
                label: "Monto del préstamo",
                prefix: "\$ ",
                formatter: ThousandsFormatter(),
              ),
              const SizedBox(height: 16),
              _buildTextField(
                controller: _plazoController,
                label: "Plazo (años)",
                suffix: " años",
                keyboardType: TextInputType.number,
              ),
              const SizedBox(height: 16),
              _buildTextField(
                controller: _tasaController,
                label: "Tasa de interés anual",
                suffix: " %",
                keyboardType: const TextInputType.numberWithOptions(decimal: true),
              ),
              const SizedBox(height: 16),

              // Tipo de cuota
              DropdownButtonFormField<String>(
                value: _tipoCuota,
                decoration: const InputDecoration(
                  labelText: "Tipo de cuota",
                  border: OutlineInputBorder(),
                ),
                items: const [
                  DropdownMenuItem(value: "Francesa", child: Text("Francesa (cuota fija)")),
                  DropdownMenuItem(value: "Alemánica", child: Text("Alemánica (capital constante)")),
                ],
                onChanged: (v) => setState(() => _tipoCuota = v!),
              ),
              const SizedBox(height: 32),

              // BOTÓN CALCULAR
              SizedBox(
                width: double.infinity,
                height: 56,
                child: ElevatedButton(
                  style: ElevatedButton.styleFrom(
                    backgroundColor: const Color(0xFF16A34A),
                    foregroundColor: Colors.white,
                    shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
                  ),
                  onPressed: _calcular,
                  child: const Text("CALCULAR", style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
                ),
              ),
              const SizedBox(height: 32),

              // === RESULTADOS ===
              if (_calculado) ...[
                // Cuota mensual grande
                Card(
                  color: isDark ? Colors.grey[850] : Colors.green[50],
                  child: Padding(
                    padding: const EdgeInsets.symmetric(vertical: 32, horizontal: 24),
                    child: Column(
                      children: [
                        const Text("CUOTA MENSUAL", style: TextStyle(fontSize: 14, color: Colors.grey)),
                        const SizedBox(height: 8),
                        Text(
                          "\$ ${cuotaMensual.toStringAsFixed(0).replaceAllMapped(RegExp(r'(\d{1,3})(?=(\d{3})+(?!\d))'), (m) => '${m[1]}.')}",
                          style: const TextStyle(fontSize: 36, fontWeight: FontWeight.bold, color: Color(0xFF16A34A)),
                        ),
                        const Text("(aprox.)", style: TextStyle(fontSize: 16)),
                      ],
                    ),
                  ),
                ),
                const SizedBox(height: 24),

                // Detalles
                _buildResultRow("Total intereses pagados", totalIntereses),
                _buildResultRow("Total a pagar", totalAPagar),
                const SizedBox(height: 32),

                // Acciones finales
                Row(
                  children: [
                    Expanded(
                      child: OutlinedButton.icon(
                        icon: const Icon(Icons.table_chart),
                        label: const Text("Ver tabla completa"),
                        onPressed: () {
                          // Aquí puedes navegar a pantalla con tabla amortización
                          ScaffoldMessenger.of(context).showSnackBar(
                            const SnackBar(content: Text("Próximamente")),
                          );
                        },
                      ),
                    ),
                    const SizedBox(width: 12),
                    Expanded(
                      child: OutlinedButton.icon(
                        icon: const Icon(Icons.compare_arrows),
                        label: const Text("Comparar otra"),
                        onPressed: () => setState(() => _calculado = false),
                      ),
                    ),
                  ],
                ),
              ],
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildTextField({
    required TextEditingController controller,
    required String label,
    String? prefix,
    String? suffix,
    TextInputType? keyboardType,
    List<TextInputFormatter>? formatter,
  }) {
    return TextFormField(
      controller: controller,
      keyboardType: keyboardType ?? TextInputType.number,
      inputFormatters: formatter,
      decoration: InputDecoration(
        labelText: label,
        prefixText: prefix,
        suffixText: suffix,
        border: const OutlineInputBorder(),
      ),
      validator: (v) {
        if (v == null || v.isEmpty) return "Requerido";
        if (double.tryParse(v.replaceAll(".", "")) == null) return "Número inválido";
        return null;
      },
    );
  }

  Widget _buildResultRow(String label, double value) {
    return Padding(
      padding: const EdgeInsets.symmetric(vertical: 8),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          Text(label, style: const TextStyle(fontSize: 16)),
          Text(
            "\$ ${value.toStringAsFixed(0).replaceAllMapped(RegExp(r'(\d{1,3})(?=(\d{3})+(?!\d))'), (m) => '${m[1]}.')}",
            style: const TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
          ),
        ],
      ),
    );
  }
}

// Formateador de miles chileno
class ThousandsFormatter extends TextInputFormatter {
  @override
  TextEditingValue formatEditUpdate(TextEditingValue oldValue, TextEditingValue newValue) {
    if (newValue.text.isEmpty) return newValue;
    final clean = newValue.text.replaceAll(".", "");
    if (clean.isEmpty) return newValue.copyWith(text: "");

    final number = int.tryParse(clean) ?? 0;
    final formatted = number.toString().replaceAllMapped(RegExp(r'(\d{1,3})(?=(\d{3})+(?!\d))'), (m) => '${m[1]}.');

    return TextEditingValue(
      text: formatted,
      selection: TextSelection.collapsed(offset: formatted.length),
    );
  }
}
