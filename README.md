[PIMENTEL N8N RAG PROJECT.json](https://github.com/user-attachments/files/26426838/PIMENTEL.N8N.RAG.PROJECT.json)## This is my first n8n RAG project.

The first workflow handles ingestion where new files added to Google Drive are automatically downloaded and embedded into a Supabase vector database, while the second workflow handles querying, where the n8n AI Agent searches the vector database to find relevant information and respond to user questions.

## JSON file
[Up{
  "name": "PIMENTEL N8N RAG PROJECT",
  "nodes": [
    {
      "parameters": {
        "pollTimes": {
          "item": [
            {
              "mode": "everyMinute"
            }
          ]
        },
        "triggerOn": "specificFolder",
        "folderToWatch": {
          "__rl": true,
          "value": "1hAOPz9y5URtwY0f4ZLPS4mGFH3hKZG-y",
          "mode": "list",
          "cachedResultName": "0 N8N PROJECT",
          "cachedResultUrl": "https://drive.google.com/drive/folders/1hAOPz9y5URtwY0f4ZLPS4mGFH3hKZG-y"
        },
        "event": "fileCreated",
        "options": {}
      },
      "type": "n8n-nodes-base.googleDriveTrigger",
      "typeVersion": 1,
      "position": [
        -2272,
        -1728
      ],
      "id": "25fc3e2b-a84c-444e-afe3-3dfcefc1fa4b",
      "name": "Google Drive Trigger",
      "credentials": {
        "googleDriveOAuth2Api": {
          "id": "fu5jpS5xHgwDYvZH",
          "name": "Google Drive account"
        }
      }
    },
    {
      "parameters": {
        "operation": "download",
        "fileId": {
          "__rl": true,
          "value": "={{ $json.id }}",
          "mode": "id"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.googleDrive",
      "typeVersion": 3,
      "position": [
        -2048,
        -1728
      ],
      "id": "aab72da8-8325-46f0-8064-255d1895855c",
      "name": "Download file",
      "credentials": {
        "googleDriveOAuth2Api": {
          "id": "fu5jpS5xHgwDYvZH",
          "name": "Google Drive account"
        }
      }
    },
    {
      "parameters": {
        "mode": "insert",
        "tableName": {
          "__rl": true,
          "value": "documents",
          "mode": "list",
          "cachedResultName": "documents"
        },
        "options": {
          "queryName": "match_documents"
        }
      },
      "type": "@n8n/n8n-nodes-langchain.vectorStoreSupabase",
      "typeVersion": 1.3,
      "position": [
        -1824,
        -1824
      ],
      "id": "d92b9a19-cac1-4087-955f-80b11ffc381a",
      "name": "Supabase Vector Store",
      "credentials": {
        "supabaseApi": {
          "id": "KBw2v8BxX92niK1d",
          "name": "N8N RAG PROJECT 04022026"
        }
      }
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.embeddingsOpenAi",
      "typeVersion": 1.2,
      "position": [
        -1856,
        -1600
      ],
      "id": "33452ab4-5525-46e7-ace1-dd5a590159c1",
      "name": "Embeddings OpenAI",
      "credentials": {
        "openAiApi": {
          "id": "Tdw5BnRldfJnlk6i",
          "name": "N8N RAG PROJECT 04022026"
        }
      }
    },
    {
      "parameters": {
        "dataType": "binary",
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.documentDefaultDataLoader",
      "typeVersion": 1.1,
      "position": [
        -1696,
        -1600
      ],
      "id": "c6f1a94a-6d8a-46a9-b2d7-171110d4d852",
      "name": "Default Data Loader"
    },
    {
      "parameters": {
        "options": {
          "systemMessage": "=#Overview\nYou are a helpful and intelligent information assistant that automatically ingests files from Google Drive, stores them as vector embeddings in Supabase, and answers user queries via an AI Agent through a chat interface.\n\n#Tools\nVector_Store - This will always be used when answering any questions that the user might have.\n\n#Rules\n- You must always pull information from the Vector_Store.\n- Your answers need to be concise and direct to the point."
        }
      },
      "type": "@n8n/n8n-nodes-langchain.agent",
      "typeVersion": 3.1,
      "position": [
        -992,
        -1792
      ],
      "id": "d5596221-451e-4b0c-8248-f19665d0f96a",
      "name": "AI Agent"
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.chatTrigger",
      "typeVersion": 1.4,
      "position": [
        -1264,
        -1792
      ],
      "id": "dc4e51b7-a899-4892-8815-566cf16cab18",
      "name": "When chat message received",
      "webhookId": "8a7419fa-b189-465d-91df-3342181ba02e"
    },
    {
      "parameters": {
        "model": {
          "__rl": true,
          "mode": "list",
          "value": "gpt-5-mini"
        },
        "builtInTools": {},
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatOpenAi",
      "typeVersion": 1.3,
      "position": [
        -1056,
        -1568
      ],
      "id": "662137e7-08bb-4706-b29e-8ccbdb185d13",
      "name": "OpenAI Chat Model",
      "credentials": {
        "openAiApi": {
          "id": "Tdw5BnRldfJnlk6i",
          "name": "N8N RAG PROJECT 04022026"
        }
      }
    },
    {
      "parameters": {},
      "type": "@n8n/n8n-nodes-langchain.memoryBufferWindow",
      "typeVersion": 1.3,
      "position": [
        -912,
        -1568
      ],
      "id": "76860e54-5285-4c6d-a38b-82f915873b59",
      "name": "Simple Memory"
    },
    {
      "parameters": {
        "mode": "retrieve-as-tool",
        "toolDescription": "Work with data from the Superbase vector database",
        "tableName": {
          "__rl": true,
          "value": "documents",
          "mode": "list",
          "cachedResultName": "documents"
        },
        "topK": 10,
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.vectorStoreSupabase",
      "typeVersion": 1.3,
      "position": [
        -784,
        -1568
      ],
      "id": "5229cc11-c9de-4c30-8572-2ce235e583a3",
      "name": "Vector_Store",
      "credentials": {
        "supabaseApi": {
          "id": "KBw2v8BxX92niK1d",
          "name": "N8N RAG PROJECT 04022026"
        }
      }
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.embeddingsOpenAi",
      "typeVersion": 1.2,
      "position": [
        -704,
        -1360
      ],
      "id": "3b9b8976-7f61-4adc-9bf6-eac558d5dbda",
      "name": "Embeddings OpenAI1",
      "credentials": {
        "openAiApi": {
          "id": "Tdw5BnRldfJnlk6i",
          "name": "N8N RAG PROJECT 04022026"
        }
      }
    },
    {
      "parameters": {
        "content": "FIRST WORKFLOW handles ingestion, where a Google Drive Trigger detects newly created files, downloads them, and processes them through OpenAI Embeddings and a Default Data Loader before storing them into a Supabase Vector Store.\n\n",
        "height": 448,
        "width": 896
      },
      "type": "n8n-nodes-base.stickyNote",
      "position": [
        -2368,
        -1872
      ],
      "typeVersion": 1,
      "id": "a108333d-f1c0-4708-9c82-38866f00d9f4",
      "name": "Sticky Note"
    },
    {
      "parameters": {
        "content": "SECOND WORKFLOW handles querying, where a chat trigger receives user messages and passes them to an AI Agent powered by an OpenAI Chat Model and Simple Memory, which then searches the Vector Store using OpenAI Embeddings across items to retrieve relevant information and respond to the user.",
        "height": 704,
        "width": 896
      },
      "type": "n8n-nodes-base.stickyNote",
      "position": [
        -1360,
        -1872
      ],
      "typeVersion": 1,
      "id": "69f827d4-542c-477e-ae6f-454b7ba34afa",
      "name": "Sticky Note1"
    }
  ],
  "pinData": {},
  "connections": {
    "Google Drive Trigger": {
      "main": [
        [
          {
            "node": "Download file",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Download file": {
      "main": [
        [
          {
            "node": "Supabase Vector Store",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Embeddings OpenAI": {
      "ai_embedding": [
        [
          {
            "node": "Supabase Vector Store",
            "type": "ai_embedding",
            "index": 0
          }
        ]
      ]
    },
    "Default Data Loader": {
      "ai_document": [
        [
          {
            "node": "Supabase Vector Store",
            "type": "ai_document",
            "index": 0
          }
        ]
      ]
    },
    "AI Agent": {
      "main": [
        []
      ]
    },
    "When chat message received": {
      "main": [
        [
          {
            "node": "AI Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Simple Memory": {
      "ai_memory": [
        [
          {
            "node": "AI Agent",
            "type": "ai_memory",
            "index": 0
          }
        ]
      ]
    },
    "Vector_Store": {
      "ai_tool": [
        [
          {
            "node": "AI Agent",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    },
    "Embeddings OpenAI1": {
      "ai_embedding": [
        [
          {
            "node": "Vector_Store",
            "type": "ai_embedding",
            "index": 0
          }
        ]
      ]
    },
    "OpenAI Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "AI Agent",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": true,
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate"
  },
  "versionId": "d0411217-a4de-4556-820e-d22bbe3a18a4",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "d8eee17be80b789b55e1e0f73e1d1a0a62d102c14f79d126b69b0910a43281d2"
  },
  "id": "skUrISomJ9k4CQsR",
  "tags": []
}loading PIMENTEL N8N RAG PROJECT.json…]()


## Workflow Preview

<img width="1146" height="396" alt="Screenshot 2026-04-02 105020" src="https://github.com/user-attachments/assets/ebf49543-63e9-4ba2-995a-82b61ff718f0" />
