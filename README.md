# Projeto-CX-SAM
import os
from pathlib import Path
import zipfile
import shutil

# Estrutura do projeto
project_structure = {
    "meu-projeto-base": {
        "README.md": "# Projeto Base: React Native + Node.js\n\nEste projeto é um modelo inicial com:\n\n- **Mobile**: React Native com Expo.\n- **Backend**: Node.js com Express.\n\n## Como usar\n\n```bash\ncd backend\nnpm install\nnpm run dev\n\ncd ../mobile\nnpm install\nnpx expo start\n```\n",
        "backend": {
            "package.json": '''{
  "name": "backend",
  "version": "1.0.0",
  "main": "src/app.js",
  "scripts": {
    "dev": "nodemon src/app.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "dotenv": "^16.0.3",
    "express": "^4.18.2"
  },
  "devDependencies": {
    "nodemon": "^2.0.22"
  }
}''',
            ".env": "PORT=3000",
            ".gitignore": "node_modules/\n.env",
            "src": {
                "app.js": '''const express = require('express');
const cors = require('cors');
require('dotenv').config();

const app = express();
app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('API online 🚀');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});''',
                "controllers": {},
                "routes": {},
                "models": {}
            }
        },
        "mobile": {
            "package.json": '''{
  "main": "src/App.js"
}''',
            ".gitignore": "node_modules/\n.expo/\n.expo-shared/",
            "app.json": "{\n  \"name\": \"mobile\",\n  \"slug\": \"mobile\"\n}",
            "README.md": "# Mobile - React Native com Expo",
            "src": {
                "App.js": '''import React from 'react';
import { SafeAreaView } from 'react-native';
import Home from './screens/Home';

export default function App() {
  return (
    <SafeAreaView style={{ flex: 1 }}>
      <Home />
    </SafeAreaView>
  );
}''',
                "screens": {
                    "Home.js": '''import React, { useEffect, useState } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import api from '../services/api';

export default function Home() {
  const [message, setMessage] = useState('');

  useEffect(() => {
    api.get('/')
      .then(res => setMessage(res.data))
      .catch(err => setMessage('Erro ao conectar com a API'));
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.text}>{message}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1, justifyContent: 'center', alignItems: 'center',
  },
  text: {
    fontSize: 20,
  },
});'''
                },
                "services": {
                    "api.js": '''import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:3000',
});

export default api;'''
                }
            }
        }
    }
}

# Criar arquivos e diretórios
def create_structure(base_path, structure):
    for name, content in structure.items():
        path = base_path / name
        if isinstance(content, dict):
            path.mkdir(parents=True, exist_ok=True)
            create_structure(path, content)
        else:
            path.write_text(content, encoding='utf-8')

# Gerar pasta temporária
base_path = Path("meu-projeto-base")
if base_path.exists():
    shutil.rmtree(base_path)

create_structure(Path("."), project_structure)

# Compactar em ZIP
zip_name = "projeto-base-react-native-node.zip"
with zipfile.ZipFile(zip_name, "w", zipfile.ZIP_DEFLATED) as zipf:
    for root, dirs, files in os.walk(base_path):
        for file in files:
            full_path = Path(root) / file
            zipf.write(full_path, full_path.relative_to("."))

print(f"Projeto gerado e salvo como {zip_name}")

