Neste lugar deixarei o codigo na integra em que ouve minha participação direta\
Parte 1: escolha e aleatorização dos pcd:

//funcao escolha dos pcd\
function selecionarNumeros() 
{

    var input = document.getElementById("vagas").value;
    if(input==''){
        var numeros=-1;
    }
    
    else{
    numeros = input.split(",").map(function(item) {
    return parseInt(item.trim());
    })};

    //checar se e inteiro
    if(numeros!=-1){
    for (var i = 0; i < numeros.length; i++) {
        if (isNaN(numeros[i])|| temDoisValoresIguais(numeros)) {
            alert("O valor digitado não é um número válido.");
            return -1;
        }
    }
    }

    

Para a geração do pdf foi ultilizada a biblioteca:
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
\
function gerarPDF(numeros, vaga, data, hora) {

    const { jsPDF } = window.jspdf;
    const doc = new jsPDF();
    // Adiciona um título dentro de uma caixa e o centraliza
    const title = "Tabela de Números Aleatórios";
    const title2 = `Data: ${data} Hora: ${hora}`;
    doc.setFontSize(16);
    const pageWidth = doc.internal.pageSize.getWidth();
    const titleWidth = doc.getTextWidth(title[0], title[1]);
    const titleX = (pageWidth - titleWidth) / 4;
    const titleX2 = (pageWidth - titleWidth) / 1.5;
    doc.rect(10, 10, 190, 10); // Desenha um retângulo para o título
    doc.text(title, titleX, 18);
    doc.setFontSize(12);
    doc.text(title2, titleX2, 18);


    // Adiciona a legenda fora da caixa
    const legendTitle = "Legenda:";
    doc.setFontSize(14);
    doc.text(legendTitle, 12, 36);

    // Adiciona a caixa da legenda
    const legendLine1 = "Apartamentos";
    const legendLine2 = "Vagas";
    doc.setFontSize(12);

    // Define a largura da caixa da legenda com base nos textos
    const maxWidth = Math.max(doc.getTextWidth(legendLine1), doc.getTextWidth(legendLine2)) + 9; // 10 para padding

    // Define a posição inicial do texto da legenda
    const legendX = 35;
    const legendY = 35; // Ajusta para ficar abaixo da linha "Legenda:"
    ;
    // Ajuste a posição da linha divisória (0.5 = no meio, 0.4 = mais à esquerda, 0.6 = mais à direita)
    const divisorRatio = 1;
    const lineDivisorX = legendX + maxWidth * divisorRatio;

    // Desenha a caixa da legenda
    doc.rect(legendX - 2, legendY - 6, maxWidth * 2, 10); // Multiplica por 2 para acomodar ambos os textos

    // Adiciona os textos da legenda
    doc.text(legendLine1, legendX + 3, legendY);
    doc.text(legendLine2, lineDivisorX + maxWidth / 6, legendY);

    // Adiciona a linha divisória entre os textos da legenda
    doc.line(lineDivisorX, legendY - 6, lineDivisorX, legendY + 4);

    // Configuração das tabelas
    const columnCount = 4; // Ajustando para 4 colunas
    const cellHeight = 6; // Altura da célula para adicionar a caixa
    const cellPadding = 2; // Espaçamento dentro da célula
    const columnSpacing = 5; // Espaçamento entre as colunas

    // Calcular a largura total das caixas com espaçamento
    const tableWidth = (pageWidth - 20 - (columnSpacing * (columnCount - 1))) / columnCount;
    const rowCount = Math.ceil(numeros.length / columnCount);

    let x = 10;
    let y = 66;
    let index = 0;
    doc.setFontSize(16);
   
    for (let col = 0; col < columnCount; col++) {
        y = 66;/
        for (let row = 0; row < rowCount; row++) {
            if (index >= numeros.length) break;
            const numero = numeros[index];
            const vagas = vaga[index];
            const texto1 = `${numero}`;
            const texto2 = `${vagas}`;

            doc.rect(x, y - cellHeight + cellPadding, tableWidth, cellHeight); // Desenha a caixa em torno do número
            doc.text(texto1, x + cellPadding, y - (cellHeight / 2) + (cellPadding * 2));
            // Para posicionar o texto2 do outro lado da barra vertical, ajuste a coordenada x
            doc.text(texto2, x + (tableWidth / 2) + cellPadding, y - (cellHeight / 2) + (cellPadding * 2));
            doc.line(x + (tableWidth / 2), y - cellHeight + cellPadding, x + (tableWidth / 2), y + cellPadding); // Desenha a barra vertical
            y += cellHeight; // Mantém a altura das linhas sem espaçamento adicional
            index++;
        }
        x += tableWidth + columnSpacing; // Adiciona o espaçamento entre as colunas
    }
    doc.save('Sorteio.pdf');
}

