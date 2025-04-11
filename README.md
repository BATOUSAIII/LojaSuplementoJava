# LojaSuplementosJava

Este projeto é uma aplicação de loja de suplementos feita em Java com interface gráfica usando Swing. O sistema permite aos usuários adicionar produtos ao carrinho, visualizar o estoque, atualizar a quantidade e finalizar a compra.

## Funcionalidades:
- Exibição de produtos disponíveis
- Seleção e adição ao carrinho
- Verificação de estoque
- Finalização da compra com cálculo do total

## Tecnologias usadas:
- Java
- Swing (para a interface gráfica)
- Git & GitHub para controle de versão

## Como rodar:
1. Clone o repositório.
2. Abra o projeto no NetBeans.
3. Compile e execute a classe `LojaGUI.java`.


------------------------

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

public class LojaGUI extends JFrame {
    private JTextArea listaProdutosArea;
    private JTextArea carrinhoArea;
    private ArrayList<String> produtos;
    private ArrayList<Double> precos;
    private ArrayList<Integer> estoques;
    private ArrayList<String> carrinho;
    private ArrayList<Integer> quantidadeCarrinho; // Lista para armazenar as quantidades do carrinho
    private ArrayList<Double> totalProduto;
    private ArrayList<JLabel> estoqueLabels; // Lista para armazenar os labels de estoque

    public LojaGUI() {
        setTitle("Loja de Suplementos");
        setSize(800, 600); // Ajustando tamanho de janela
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null); // Centraliza a janela
        setResizable(false); // Impede o redimensionamento da janela

        // Inicializando as listas de produtos, preços e estoques
        produtos = new ArrayList<>();
        precos = new ArrayList<>();
        estoques = new ArrayList<>();
        carrinho = new ArrayList<>();
        quantidadeCarrinho = new ArrayList<>(); // Inicializa a lista de quantidades
        totalProduto = new ArrayList<>();
        estoqueLabels = new ArrayList<>(); // Inicializa a lista de labels de estoque

        // Produtos e variações
        produtos.add("Whey Protein - Chocolate");
        precos.add(120.0);
        estoques.add(999);

        produtos.add("Whey Protein - Morango");
        precos.add(130.0);
        estoques.add(999);

        produtos.add("Creatina");
        precos.add(70.0);
        estoques.add(999);

        produtos.add("Pré-treino");
        precos.add(95.0);
        estoques.add(999);

        // Áreas e painéis
        listaProdutosArea = new JTextArea(10, 30);
        listaProdutosArea.setEditable(false);
        atualizarLista();

        JScrollPane scrollPane = new JScrollPane(listaProdutosArea);

        // Área do carrinho
        carrinhoArea = new JTextArea(10, 25);
        carrinhoArea.setEditable(false);

        // Painel para carrinho
        JPanel carrinhoPanel = new JPanel(new BorderLayout());
        carrinhoPanel.setBorder(BorderFactory.createTitledBorder("Carrinho"));
        carrinhoPanel.add(new JScrollPane(carrinhoArea), BorderLayout.CENTER);

        // Painel de entrada (campo + botão adicionar)
        JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        JButton finalizarButton = new JButton("Finalizar Compra");
        JButton sairButton = new JButton("Sair");
        JButton resetarButton = new JButton("Resetar");

        finalizarButton.addActionListener(e -> finalizarCompra());
        sairButton.addActionListener(e -> System.exit(0));
        resetarButton.addActionListener(e -> resetarCarrinho()); // Função para resetar o carrinho

        inputPanel.add(finalizarButton);
        inputPanel.add(resetarButton); // Adicionando botão "Resetar"

        // Criando os botões visuais para os produtos
        JPanel produtoPanel = new JPanel();
        produtoPanel.setLayout(new GridLayout(produtos.size(), 1, 5, 10)); // Ajusta os espaçamentos
        produtoPanel.setBorder(BorderFactory.createTitledBorder("Escolha um Produto"));

        for (int i = 0; i < produtos.size(); i++) {
            JPanel produtoItemPanel = new JPanel();
            produtoItemPanel.setLayout(new BoxLayout(produtoItemPanel, BoxLayout.Y_AXIS)); // Ajusta a ordem vertical

            // Nome do produto (aumentando a fonte e colocando em negrito)
            JLabel produtoLabel = new JLabel(produtos.get(i));
            produtoLabel.setFont(new Font("Arial", Font.BOLD, 16)); // Aumentando o tamanho da fonte e colocando em negrito
            produtoLabel.setAlignmentX(Component.CENTER_ALIGNMENT); // Centraliza o nome do produto
            produtoItemPanel.add(produtoLabel);

            // Carregar e adicionar imagem do produto
            String caminhoImagem = "/caminho/para/imagem/" + produtos.get(i) + ".jpg"; // Substitua com o caminho correto
            ImageIcon produtoImagem = new ImageIcon(caminhoImagem); // Carrega a imagem
            produtoImagem = new ImageIcon(produtoImagem.getImage().getScaledInstance(100, 100, Image.SCALE_DEFAULT)); // Ajuste o tamanho da imagem
            JLabel imagemLabel = new JLabel(produtoImagem);
            imagemLabel.setAlignmentX(Component.CENTER_ALIGNMENT); // Centraliza a imagem
            produtoItemPanel.add(imagemLabel);

            // Painel de quantidade ao lado do nome
            JPanel quantidadePanel = new JPanel();
            quantidadePanel.setLayout(new FlowLayout(FlowLayout.CENTER)); // Centraliza o campo
            JTextField quantidadeField = new JTextField(3); // Tamanho adequado para o número
            quantidadeField.setText("1"); // Valor inicial como 1
            quantidadeField.setPreferredSize(new Dimension(40, 25)); // Ajuste do tamanho
            quantidadePanel.add(quantidadeField);
            produtoItemPanel.add(quantidadePanel);

            // Texto para o estoque com cor e opacidade
            JLabel estoqueLabel = new JLabel("Estoque: " + estoques.get(i));
            estoqueLabel.setFont(new Font("Arial", Font.PLAIN, 12)); // Tamanho da fonte menor e sem negrito
            estoqueLabel.setForeground(new Color(0, 0, 0, 128)); // Cor com opacidade de 50% (RGBA)
            estoqueLabel.setAlignmentX(Component.CENTER_ALIGNMENT); // Centraliza o texto
            estoqueLabels.add(estoqueLabel); // Adiciona o label à lista

            produtoItemPanel.add(estoqueLabel);

            // Painel do botão "Adicionar no Carrinho" com borda arredondada
            JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER)); // Alinha o botão à esquerda
            JButton produtoButton = new JButton("Adicionar no Carrinho");
            produtoButton.setPreferredSize(new Dimension(190, 30)); // Tamanho fixo para o botão
            produtoButton.setFont(new Font("Arial", Font.PLAIN, 12)); // Tamanho da fonte menor
            produtoButton.setBackground(new Color(50, 150, 255)); // Cor de fundo
            produtoButton.setForeground(Color.WHITE); // Cor do texto
            produtoButton.setFocusPainted(false); // Remove o foco do botão

            // Definindo borda arredondada para os botões
            produtoButton.setBorder(BorderFactory.createCompoundBorder(
                produtoButton.getBorder(),
                BorderFactory.createEmptyBorder(5, 15, 5, 15) // Arredondamento das bordas
            ));

            // Adiciona o botão de seleção
            int finalI = i; // Captura o índice do produto
            produtoButton.addActionListener(e -> selecionarProduto(finalI, quantidadeField)); // Ação para cada botão

            // Adiciona o botão "Adicionar no Carrinho"
            buttonPanel.add(produtoButton);
            produtoItemPanel.add(buttonPanel);

            produtoPanel.add(produtoItemPanel);
        }

        // Painel inferior com botão sair
        JPanel bottomPanel = new JPanel(new BorderLayout());
        bottomPanel.add(inputPanel, BorderLayout.CENTER);

        JPanel sairPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT));
        sairPanel.add(sairButton);

        bottomPanel.add(sairPanel, BorderLayout.SOUTH);

        // Painel principal
        JPanel mainPanel = new JPanel(new BorderLayout(10, 10));
        mainPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));
        mainPanel.add(produtoPanel, BorderLayout.CENTER); // Coloca os botões de produtos no centro
        mainPanel.add(carrinhoPanel, BorderLayout.EAST); // Coloca o carrinho à direita dos botões
        mainPanel.add(bottomPanel, BorderLayout.SOUTH);

        add(mainPanel);
    }

    private void atualizarLista() {
        StringBuilder texto = new StringBuilder("Produtos disponíveis:\n");
        for (int i = 0; i < produtos.size(); i++) {
            texto.append(i + 1).append(" - ").append(produtos.get(i))
                 .append(" - R$").append(precos.get(i))
                 .append(" - Estoque: ").append(estoques.get(i))
                 .append("\n");
        }
        listaProdutosArea.setText(texto.toString());

        // Atualizar a quantidade de estoque na interface
        for (int i = 0; i < estoqueLabels.size(); i++) {
            estoqueLabels.get(i).setText("Estoque: " + estoques.get(i));
        }
    }

    private void selecionarProduto(int produtoEscolhido, JTextField quantidadeField) {
        String produto = produtos.get(produtoEscolhido);
        double preco = precos.get(produtoEscolhido);
        int estoque = estoques.get(produtoEscolhido);

        String quantidadeTexto = quantidadeField.getText().trim();

        if (!quantidadeTexto.isEmpty()) {
            try {
                int quantidade = Integer.parseInt(quantidadeTexto);
                if (quantidade <= estoque) {
                    // Atualiza o carrinho
                    carrinho.add(produto + " x" + quantidade);
                    quantidadeCarrinho.add(quantidade); // Armazena a quantidade no carrinho
                    totalProduto.add(preco * quantidade);

                    // Atualiza o estoque
                    estoques.set(produtoEscolhido, estoque - quantidade);

                    // Atualiza a lista de produtos
                    atualizarLista();

                    // Atualiza o carrinho na interface
                    carrinhoArea.setText(""); // Limpa a área de carrinho antes de atualizar
                    for (int i = 0; i < carrinho.size(); i++) {
                        carrinhoArea.append(carrinho.get(i) + " - R$" + String.format("%.2f", totalProduto.get(i)) + "\n");
                    }

                    JOptionPane.showMessageDialog(this, "Produto adicionado ao carrinho. Estoque restante: " + (estoque - quantidade));
                } else {
                    JOptionPane.showMessageDialog(this, "Quantidade em estoque insuficiente.");
                }
            } catch (NumberFormatException e) {
                JOptionPane.showMessageDialog(this, "Digite uma quantidade válida.");
            }
        } else {
            JOptionPane.showMessageDialog(this, "Digite uma quantidade.");
        }
    }

    private void resetarCarrinho() {
        // Reseta os carrinho e as quantidades
        carrinho.clear();
        quantidadeCarrinho.clear();
        totalProduto.clear();
        carrinhoArea.setText(""); // Limpa o carrinho na interface
        JOptionPane.showMessageDialog(this, "Carrinho resetado.");
    }

    private void finalizarCompra() {
        double total = 0;
        StringBuilder resumo = new StringBuilder("Resumo da Compra:\n");

        for (int i = 0; i < carrinho.size(); i++) {
            resumo.append(carrinho.get(i))
                  .append(" - Total: R$")
                  .append(String.format("%.2f", totalProduto.get(i)))
                  .append("\n");
            total += totalProduto.get(i);
        }

        resumo.append("\nTotal Gasto: R$").append(String.format("%.2f", total));

        JOptionPane.showMessageDialog(this, resumo.toString());
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            new LojaGUI().setVisible(true);
        });
    }
}
