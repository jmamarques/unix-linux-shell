#!/bin/bash

# Verifica se foram fornecidos dois argumentos (nomes de ficheiros)
if [ "$#" -ne 2 ]; then
    echo "Usage: $0 <ficheiro1> <ficheiro2>"
    exit 1
fi

# Atribui os nomes dos ficheiros aos parâmetros
ficheiro1="$1"
ficheiro2="$2"

# Verifica se ambos os ficheiros existem
if [ ! -e "$ficheiro1" ] || [ ! -e "$ficheiro2" ]; then
    echo "Ambos os ficheiros devem existir."
    exit 1
fi

# Nome do ficheiro de saída
ficheiro_saida="resultado.txt"

# Loop sobre cada linha no segundo ficheiro
while IFS= read -r linha_ficheiro2; do
    # Obtém os primeiros 10 caracteres da linha do ficheiro 2
    prefixo_ficheiro2="${linha_ficheiro2:0:10}"

    # Filtra as linhas do primeiro ficheiro com base no prefixo do ficheiro 2
    grep "^$prefixo_ficheiro2" "$ficheiro1"

done < "$ficheiro2" > "$ficheiro_saida"

echo "Linhas filtradas foram salvas em $ficheiro_saida"
