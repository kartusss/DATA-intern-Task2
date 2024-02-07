# DATA-intern-Task2
Sentiment Analysis

{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 1,
   "id": "fbf0c96a",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:38.462433Z",
     "iopub.status.busy": "2024-02-04T13:04:38.461582Z",
     "iopub.status.idle": "2024-02-04T13:04:39.500059Z",
     "shell.execute_reply": "2024-02-04T13:04:39.498502Z"
    },
    "papermill": {
     "duration": 1.056223,
     "end_time": "2024-02-04T13:04:39.502819",
     "exception": false,
     "start_time": "2024-02-04T13:04:38.446596",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "/kaggle/input/sentiment-analysis-dataset/training.1600000.processed.noemoticon.csv\n",
      "/kaggle/input/sentiment-analysis-dataset/train.csv\n",
      "/kaggle/input/sentiment-analysis-dataset/testdata.manual.2009.06.14.csv\n",
      "/kaggle/input/sentiment-analysis-dataset/test.csv\n"
     ]
    }
   ],
   "source": [
    "# This Python 3 environment comes with many helpful analytics libraries installed\n",
    "# It is defined by the kaggle/python Docker image: https://github.com/kaggle/docker-python\n",
    "# For example, here's several helpful packages to load\n",
    "\n",
    "import numpy as np # linear algebra\n",
    "import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)\n",
    "\n",
    "# Input data files are available in the read-only \"../input/\" directory\n",
    "# For example, running this (by clicking run or pressing Shift+Enter) will list all files under the input directory\n",
    "\n",
    "import os\n",
    "for dirname, _, filenames in os.walk('/kaggle/input'):\n",
    "    for filename in filenames:\n",
    "        print(os.path.join(dirname, filename))\n",
    "\n",
    "# You can write up to 20GB to the current directory (/kaggle/working/) that gets preserved as output when you create a version using \"Save & Run All\" \n",
    "# You can also write temporary files to /kaggle/temp/, but they won't be saved outside of the current session"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "id": "4a113e03",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:39.530241Z",
     "iopub.status.busy": "2024-02-04T13:04:39.529660Z",
     "iopub.status.idle": "2024-02-04T13:04:42.363330Z",
     "shell.execute_reply": "2024-02-04T13:04:42.361997Z"
    },
    "papermill": {
     "duration": 2.851329,
     "end_time": "2024-02-04T13:04:42.366929",
     "exception": false,
     "start_time": "2024-02-04T13:04:39.515600",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "import os\n",
    "import numpy as np\n",
    "import pandas as pd\n",
    "import matplotlib.pyplot as plt\n",
    "import seaborn as sns\n",
    "from nltk.corpus import stopwords\n",
    "from string import punctuation\n",
    "from nltk.tokenize import word_tokenize\n",
    "from nltk.stem import LancasterStemmer\n",
    "from string import punctuation\n",
    "from nltk.corpus import stopwords\n",
    "from nltk.tokenize import word_tokenize\n",
    "from nltk.stem import LancasterStemmer\n",
    "from nltk.stem.wordnet import WordNetLemmatizer\n",
    "import re\n",
    "import warnings\n",
    "warnings.filterwarnings('ignore')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "id": "4bd0b3ec",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:42.395621Z",
     "iopub.status.busy": "2024-02-04T13:04:42.395233Z",
     "iopub.status.idle": "2024-02-04T13:04:47.439570Z",
     "shell.execute_reply": "2024-02-04T13:04:47.438744Z"
    },
    "papermill": {
     "duration": 5.062277,
     "end_time": "2024-02-04T13:04:47.442197",
     "exception": false,
     "start_time": "2024-02-04T13:04:42.379920",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Sentiment</th>\n",
       "      <th>id</th>\n",
       "      <th>date</th>\n",
       "      <th>query</th>\n",
       "      <th>user</th>\n",
       "      <th>text</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>1467810672</td>\n",
       "      <td>Mon Apr 06 22:19:49 PDT 2009</td>\n",
       "      <td>NO_QUERY</td>\n",
       "      <td>scotthamilton</td>\n",
       "      <td>is upset that he can't update his Facebook by ...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>0</td>\n",
       "      <td>1467810917</td>\n",
       "      <td>Mon Apr 06 22:19:53 PDT 2009</td>\n",
       "      <td>NO_QUERY</td>\n",
       "      <td>mattycus</td>\n",
       "      <td>@Kenichan I dived many times for the ball. Man...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>0</td>\n",
       "      <td>1467811184</td>\n",
       "      <td>Mon Apr 06 22:19:57 PDT 2009</td>\n",
       "      <td>NO_QUERY</td>\n",
       "      <td>ElleCTF</td>\n",
       "      <td>my whole body feels itchy and like its on fire</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>0</td>\n",
       "      <td>1467811193</td>\n",
       "      <td>Mon Apr 06 22:19:57 PDT 2009</td>\n",
       "      <td>NO_QUERY</td>\n",
       "      <td>Karoli</td>\n",
       "      <td>@nationwideclass no, it's not behaving at all....</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0</td>\n",
       "      <td>1467811372</td>\n",
       "      <td>Mon Apr 06 22:20:00 PDT 2009</td>\n",
       "      <td>NO_QUERY</td>\n",
       "      <td>joy_wolf</td>\n",
       "      <td>@Kwesidei not the whole crew</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Sentiment          id                          date     query  \\\n",
       "0          0  1467810672  Mon Apr 06 22:19:49 PDT 2009  NO_QUERY   \n",
       "1          0  1467810917  Mon Apr 06 22:19:53 PDT 2009  NO_QUERY   \n",
       "2          0  1467811184  Mon Apr 06 22:19:57 PDT 2009  NO_QUERY   \n",
       "3          0  1467811193  Mon Apr 06 22:19:57 PDT 2009  NO_QUERY   \n",
       "4          0  1467811372  Mon Apr 06 22:20:00 PDT 2009  NO_QUERY   \n",
       "\n",
       "            user                                               text  \n",
       "0  scotthamilton  is upset that he can't update his Facebook by ...  \n",
       "1       mattycus  @Kenichan I dived many times for the ball. Man...  \n",
       "2        ElleCTF    my whole body feels itchy and like its on fire   \n",
       "3         Karoli  @nationwideclass no, it's not behaving at all....  \n",
       "4       joy_wolf                      @Kwesidei not the whole crew   "
      ]
     },
     "execution_count": 3,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df = pd.read_csv('/kaggle/input/sentiment-analysis-dataset/training.1600000.processed.noemoticon.csv',\n",
    "                 delimiter=',', encoding='ISO-8859-1')\n",
    "df.columns = ['Sentiment','id','date','query','user','text']\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "id": "2a0610fc",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:47.470940Z",
     "iopub.status.busy": "2024-02-04T13:04:47.470547Z",
     "iopub.status.idle": "2024-02-04T13:04:47.512819Z",
     "shell.execute_reply": "2024-02-04T13:04:47.511534Z"
    },
    "papermill": {
     "duration": 0.060323,
     "end_time": "2024-02-04T13:04:47.515627",
     "exception": false,
     "start_time": "2024-02-04T13:04:47.455304",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "df = df[['Sentiment','text']]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "id": "58b806fa",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:47.543691Z",
     "iopub.status.busy": "2024-02-04T13:04:47.543263Z",
     "iopub.status.idle": "2024-02-04T13:04:47.550369Z",
     "shell.execute_reply": "2024-02-04T13:04:47.549246Z"
    },
    "papermill": {
     "duration": 0.023947,
     "end_time": "2024-02-04T13:04:47.552764",
     "exception": false,
     "start_time": "2024-02-04T13:04:47.528817",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "Index(['Sentiment', 'text'], dtype='object')"
      ]
     },
     "execution_count": 5,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df.columns"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "id": "eda069d9",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:47.580885Z",
     "iopub.status.busy": "2024-02-04T13:04:47.580442Z",
     "iopub.status.idle": "2024-02-04T13:04:47.608126Z",
     "shell.execute_reply": "2024-02-04T13:04:47.606987Z"
    },
    "papermill": {
     "duration": 0.044991,
     "end_time": "2024-02-04T13:04:47.610849",
     "exception": false,
     "start_time": "2024-02-04T13:04:47.565858",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "Sentiment\n",
       "0    799996\n",
       "4    248576\n",
       "Name: count, dtype: int64"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df.Sentiment.value_counts()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "id": "c83d27b8",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:47.639710Z",
     "iopub.status.busy": "2024-02-04T13:04:47.639282Z",
     "iopub.status.idle": "2024-02-04T13:04:47.658782Z",
     "shell.execute_reply": "2024-02-04T13:04:47.657524Z"
    },
    "papermill": {
     "duration": 0.037086,
     "end_time": "2024-02-04T13:04:47.661722",
     "exception": false,
     "start_time": "2024-02-04T13:04:47.624636",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "df['Sentiment'] = df['Sentiment'].replace({4:1})"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "id": "9bc3eabb",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:47.691102Z",
     "iopub.status.busy": "2024-02-04T13:04:47.690368Z",
     "iopub.status.idle": "2024-02-04T13:04:47.898507Z",
     "shell.execute_reply": "2024-02-04T13:04:47.897305Z"
    },
    "papermill": {
     "duration": 0.225657,
     "end_time": "2024-02-04T13:04:47.901182",
     "exception": false,
     "start_time": "2024-02-04T13:04:47.675525",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAjcAAAGzCAYAAADT4Tb9AAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjcuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8WgzjOAAAACXBIWXMAAA9hAAAPYQGoP6dpAAAqW0lEQVR4nO3de1iUdf7/8RegDHgANU5CLKhpahoaKuEpNZTMdZfa1NVaSNOyzDXZSs2EtVS2LY39rqZZqZtXbn6rr/VNvTyEWquysWnqtqvmKfWrcvAAGCbYzP37w5+zOwso54GPz8d1zXUtn7nvmfcg1/LsnntuPCzLsgQAAGAIT3cPAAAAUJOIGwAAYBTiBgAAGIW4AQAARiFuAACAUYgbAABgFOIGAAAYhbgBAABGIW4AAIBRiBsANSYyMlKPPvqou8dwcejQIQ0ZMkT+/v7y8PDQxx9/7O6RKmXFihXy8PDQd9995+5RgAaDuAFq2JEjR/TEE0+obdu28vHxkZ+fn/r06aM//OEP+uGHH9w9niTpjTfe0IoVKyq8vYeHh/Pm6emp0NBQDRkyRNu2bauReU6fPq3f/va32rNnT4083r9LSkrS3//+d82dO1crV65Ujx49yt02Ly9PU6ZMUceOHeXr66ugoCD16tVL06ZN0/fff1/js/27efPmNbjw+neV/ZkCapMHf1sKqDnr1q3TiBEjZLPZlJiYqC5duqikpETbt2/XRx99pEcffVRLly5195jq0qWLAgICKhwnHh4eGjx4sBITE2VZlo4dO6Y33nhDubm5WrdunYYOHSrp6pGbAQMGVPqX3FdffaWePXtq+fLlNXrk54cfflCTJk00c+ZMzZkz57rbnj9/Xt27d1dhYaHGjRunjh076ty5c9q3b5/Wrl2rffv2KTIyssZm+0/NmjXTQw89VOp7Z7fbdeXKFdlsNnl4eNTa81dXZX+mgNrUyN0DAKY4duyYfvnLXyoiIkJbtmxR69atnfdNmjRJhw8f1rp169w4YfV06NBBjzzyiPPrBx54QHfeeafS09OdcVPf5OXlSZJatGhxw23feecdnThxQjt27FDv3r1d7issLJS3t3dtjHhDXl5e8vLycstzAw2WBaBGTJw40ZJk7dixo0LbX7lyxXrppZestm3bWt7e3lZERIQ1Y8YM6/Llyy7bSbJSU1NL7R8REWElJSU5v16+fLklydq+fbs1depUKyAgwGrSpImVkJBg5ebmuuwnyeV2zz33XHdWSdakSZNKrQcEBFjt27cvdybLsqwjR45YDz30kNWyZUvL19fXiomJsdauXeu8f+vWraXmkWQtX778ujPt3r3buu+++6zmzZtbTZs2tQYNGmRlZmY6709NTS31mBEREeU+3hNPPGF5eXlZdrv9us97zV//+lcrPj7e8vPzs3x9fa3+/ftb27dvd9nm2gyHDh2ykpKSLH9/f8vPz8969NFHraKiIud2Zb3+a9/Ha/+ux44dc24fERFhDRs2zNq6dasVHR1t+fj4WF26dLG2bt1qWZZlffTRR1aXLl0sm81m3XXXXdbu3btLzb9//37rF7/4hdWyZUvLZrNZ0dHR1ieffOKyTW3+TAG1iXNugBry6aefqm3btqX+q78848ePV0pKiu666y69/vrruueee5SWlqZf/vKX1Zpj8uTJ2rt3r1JTU/Xkk0/q008/1dNPP+28Pz09Xbfeeqs6duyolStXauXKlZo5c2aln+fChQu6cOGCbrnllnK3ycnJUe/evbVx40Y99dRTmjt3ri5fvqyf/exnWrNmjSSpU6dOeumllyRJjz/+uHOm/v37l/u4//jHP9SvXz/t3btXzz//vGbNmqVjx45pwIAB+vLLLyVJDz74oF5//XVJ0ujRo7Vy5Uqlp6eX+5gRERGy2+1auXLlDV/7li1b1L9/fxUWFio1NVXz5s1Tfn6+Bg0apKysrFLbjxw5UhcvXlRaWppGjhypFStWaPbs2c77V65cKZvNpn79+jlf/xNPPHHdGQ4fPqwxY8Zo+PDhSktL04ULFzR8+HC99957mjp1qh555BHNnj1bR44c0ciRI+VwOFy+f3fffbf279+v6dOna/78+WratKkSEhKc/y7/rq5+poAa4+66AkxQUFBgSbJ+/vOfV2j7PXv2WJKs8ePHu6w/++yzliRry5YtzjVV8shNXFyc5XA4nOtTp061vLy8rPz8fOfaHXfcUan/spZkPfbYY1ZeXp6Vm5trffnll9a9995rSbLmz59f7kzPPPOMJcn6y1/+4ly7ePGi1aZNGysyMtJ5lORvf/tbhY7WXJOQkGB5e3tbR44cca6dPn3aat68udW/f3/n2rFjxyxJ1quvvnrDx8zOzrYCAwMtSVbHjh2tiRMnWqtWrXL5vlmWZTkcDqt9+/ZWfHy8y/f50qVLVps2bazBgwc7164duRk3bpzLYzzwwAPWLbfc4rLWtGnTUke9LKv8IzeSrJ07dzrXNm7caEmyfH19rePHjzvX33zzTUuS86iOZVnWvffea3Xt2tXlKKHD4bB69+7tciSuNn+mgNrEkRugBhQWFkqSmjdvXqHt169fL0lKTk52Wf/Nb34jSdU6N+fxxx93OfG0X79+stvtOn78eJUfU7p6TkpgYKCCgoIUExOjHTt2KDk5Wc8880y5+6xfv169evVS3759nWvNmjXT448/ru+++07//Oc/Kz2H3W7Xpk2blJCQoLZt2zrXW7durTFjxmj79u3Of4/KCA4O1t69ezVx4kRduHBBS5Ys0ZgxYxQUFKSXX35Z1v//7MWePXt06NAhjRkzRufOndPZs2d19uxZFRUV6d5779UXX3zhcpREkiZOnOjydb9+/XTu3LkqzXlN586dFRsb6/w6JiZGkjRo0CD95Cc/KbV+9OhRSVdPnN6yZYvzaNK1+c+dO6f4+HgdOnRIp06dcnmu2vqZAmrLTR03X3zxhYYPH67Q0NAqX//Csiy99tpr6tChg2w2m8LCwjR37tyaHxb1mp+fnyTp4sWLFdr++PHj8vT01G233eayHhISohYtWlTrl8a//2KTpJYtW0q6+jZSdfz85z/X5s2b9dlnn+nLL7/U2bNnNX/+fHl6lv9/I8ePH9ftt99ear1Tp07O+ysrLy9Ply5dKvdxHQ6HTp48WenHla4G0uLFi3XmzBkdPHhQ//Vf/6XAwEClpKTonXfekXT1ujnS1Y+YBwYGutzefvttFRcXq6CgwOVxa+Pf5D8f09/fX5IUHh5e5vq15zp8+LAsy9KsWbNKzZ+amipJys3NrfX5gdp0U39aqqioSFFRURo3bpwefPDBKj3GlClTtGnTJr322mvq2rWrzp8/r/Pnz9fwpKjv/Pz8FBoaqm+++aZS+1Xno712u73M9fI+WWNV86oPt956q+Li4qr1GA2Fh4eHOnTooA4dOmjYsGFq37693nvvPY0fP955VObVV19Vt27dyty/WbNmLl/Xxr9JeY95o+e6Nv+zzz6r+Pj4Mrf9z+iurZ8poLbc1HEzdOjQ636Etbi4WDNnztSf//xn5efnq0uXLnrllVc0YMAASdL+/fu1ePFiffPNN87/imzTpk1djI566Kc//amWLl2qzMxMl7cLyhIRESGHw6FDhw45j2JIV0/Azc/PV0REhHOtZcuWys/Pd9m/pKREZ86cqfKsdXW9lIiICB08eLDU+oEDB5z3V3aewMBANWnSpNzH9fT0LHX0ojratm2rli1bOr/f7dq1k3Q1aGsy9urq3+TaW3mNGzdukPMDFXFTvy11I08//bQyMzP1/vvva9++fRoxYoTuu+8+52Hpa5+OWbt2rdq0aaPIyEiNHz+eIzc3qeeff15NmzbV+PHjlZOTU+r+I0eO6A9/+IMk6f7775ekUp/eWbBggSRp2LBhzrV27drpiy++cNlu6dKl5R65qYimTZuWCqbacP/99ysrK0uZmZnOtaKiIi1dulSRkZHq3Lmzcx5JFZrJy8tLQ4YM0SeffOLyJwlycnK0atUq9e3b1/k2YWV8+eWXKioqKrWelZWlc+fOOf8DJjo6Wu3atdNrr71W5lWLr11bp7Lq6t8kKChIAwYM0JtvvllmINf3+YGKuKmP3FzPiRMntHz5cp04cUKhoaGSrh7G3bBhg5YvX6558+bp6NGjOn78uD744AO9++67stvtmjp1qh566CFt2bLFza8Ada1du3ZatWqVRo0apU6dOrlcoXjnzp364IMPnFffjYqKUlJSkpYuXar8/Hzdc889ysrK0p/+9CclJCRo4MCBzscdP368Jk6cqF/84hcaPHiw9u7dq40bNyogIKDKs0ZHR2vx4sWaM2eObrvtNgUFBWnQoEHV/RaUMn36dP35z3/W0KFD9etf/1qtWrXSn/70Jx07dkwfffSR83yddu3aqUWLFlqyZImaN2+upk2bKiYmptwjoXPmzNHmzZvVt29fPfXUU2rUqJHefPNNFRcX6/e//32VZl25cqXee+89PfDAA4qOjpa3t7f279+vZcuWycfHRy+88IIkydPTU2+//baGDh2qO+64Q2PHjlVYWJhOnTqlrVu3ys/PT59++mmlnz86OlqfffaZFixYoNDQULVp08Z5MnBNW7Rokfr27auuXbtqwoQJatu2rXJycpSZman/+7//0969eyv9mHX1MwVUiBs/qVWvSLLWrFnj/Hrt2rWWJKtp06Yut0aNGlkjR460LMuyJkyYYEmyDh486Nxv165dliTrwIEDdf0SUE98++231oQJE6zIyEjL29vbat68udWnTx/rj3/8o8tHb69c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",
      "text/plain": [
       "<Figure size 640x480 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "sns.countplot(df[\"Sentiment\"])\n",
    "plt.title(\"Count Plot of Sentiment\")\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "id": "26bed06c",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:47.930177Z",
     "iopub.status.busy": "2024-02-04T13:04:47.929715Z",
     "iopub.status.idle": "2024-02-04T13:04:48.061997Z",
     "shell.execute_reply": "2024-02-04T13:04:48.060551Z"
    },
    "papermill": {
     "duration": 0.150084,
     "end_time": "2024-02-04T13:04:48.064907",
     "exception": false,
     "start_time": "2024-02-04T13:04:47.914823",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0"
      ]
     },
     "execution_count": 9,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df.isna().sum().sum()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "id": "317d6f79",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:48.094371Z",
     "iopub.status.busy": "2024-02-04T13:04:48.093857Z",
     "iopub.status.idle": "2024-02-04T13:04:48.099480Z",
     "shell.execute_reply": "2024-02-04T13:04:48.098178Z"
    },
    "papermill": {
     "duration": 0.023082,
     "end_time": "2024-02-04T13:04:48.101970",
     "exception": false,
     "start_time": "2024-02-04T13:04:48.078888",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "from sklearn.utils import resample"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "id": "0bd5c92c",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:48.130960Z",
     "iopub.status.busy": "2024-02-04T13:04:48.130513Z",
     "iopub.status.idle": "2024-02-04T13:04:48.196339Z",
     "shell.execute_reply": "2024-02-04T13:04:48.195105Z"
    },
    "papermill": {
     "duration": 0.083425,
     "end_time": "2024-02-04T13:04:48.199116",
     "exception": false,
     "start_time": "2024-02-04T13:04:48.115691",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "## majority class 0\n",
    "df_majority = df[df['Sentiment']==0]\n",
    "## minority class 1\n",
    "df_minority = df[df['Sentiment']==1]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "id": "7a0f430f",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:48.228036Z",
     "iopub.status.busy": "2024-02-04T13:04:48.227621Z",
     "iopub.status.idle": "2024-02-04T13:04:48.235318Z",
     "shell.execute_reply": "2024-02-04T13:04:48.233760Z"
    },
    "papermill": {
     "duration": 0.024725,
     "end_time": "2024-02-04T13:04:48.237562",
     "exception": false,
     "start_time": "2024-02-04T13:04:48.212837",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(248576, 2)"
      ]
     },
     "execution_count": 12,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_minority.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "id": "3c57ca61",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:48.266644Z",
     "iopub.status.busy": "2024-02-04T13:04:48.266225Z",
     "iopub.status.idle": "2024-02-04T13:04:48.344724Z",
     "shell.execute_reply": "2024-02-04T13:04:48.343408Z"
    },
    "papermill": {
     "duration": 0.096089,
     "end_time": "2024-02-04T13:04:48.347437",
     "exception": false,
     "start_time": "2024-02-04T13:04:48.251348",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "# downsample the majority class\n",
    "df_majority_downsampled = resample(df_majority, \n",
    "                                 replace=False,   \n",
    "                                 n_samples=len(df_minority),    \n",
    "                                 random_state=1234) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "id": "74e95654",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:48.378171Z",
     "iopub.status.busy": "2024-02-04T13:04:48.376964Z",
     "iopub.status.idle": "2024-02-04T13:04:48.416427Z",
     "shell.execute_reply": "2024-02-04T13:04:48.415267Z"
    },
    "papermill": {
     "duration": 0.057711,
     "end_time": "2024-02-04T13:04:48.419008",
     "exception": false,
     "start_time": "2024-02-04T13:04:48.361297",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Sentiment</th>\n",
       "      <th>text</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>Wow slept for almost 12hours. Sleepy me!! Uni ...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>0</td>\n",
       "      <td>gets bored with an idea too easily ... like tw...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>0</td>\n",
       "      <td>To my girls - sorry i've been a homebody latel...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>0</td>\n",
       "      <td>BK once again for the weekend...If it wasnt fo...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0</td>\n",
       "      <td>@DonnieWahlberg Now why didn't you do that las...</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Sentiment                                               text\n",
       "0          0  Wow slept for almost 12hours. Sleepy me!! Uni ...\n",
       "1          0  gets bored with an idea too easily ... like tw...\n",
       "2          0  To my girls - sorry i've been a homebody latel...\n",
       "3          0  BK once again for the weekend...If it wasnt fo...\n",
       "4          0  @DonnieWahlberg Now why didn't you do that las..."
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df = pd.concat([df_majority_downsampled, df_minority], ignore_index=True)\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "id": "30c392d9",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:48.451003Z",
     "iopub.status.busy": "2024-02-04T13:04:48.449944Z",
     "iopub.status.idle": "2024-02-04T13:04:48.612276Z",
     "shell.execute_reply": "2024-02-04T13:04:48.611031Z"
    },
    "papermill": {
     "duration": 0.181066,
     "end_time": "2024-02-04T13:04:48.615289",
     "exception": false,
     "start_time": "2024-02-04T13:04:48.434223",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "image/png": "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u5eWHrMJEyZY/v7+1vvvv99oruF/L02dqWrOZz9x4sQlj/PGjRstSU2+P/Bt4Oo/oI3weDySpM6dOzer/o033pAkZWVleY0//PDDknRZe6+mT58uPz8/+/ltt92muro6ff75561eU5L+8Ic/KCIiQpGRkUpMTNS7776rrKwsZWZmXvA1b7zxhoYOHapbb73VHgsNDdX06dP12Wef6cMPP2xxH3V1dXrzzTc1evRo9enTxx7v3r27fvrTn+qdd96xfx6mBAcH2/+urq7W8ePHdcstt8iyLP31r39tVD9z5kyv5/n5+ZKkn//8517jGRkZXs8ty9Krr76q++67T5Zl6fjx4/bD5XKpqqpKe/bsaXH/9fX1eu2113Tfffc1efXj+f97+brmfPbg4GA5HA7t2LFDJ06caHKdhn1tmzdv1tmzZ1v8GYDLRagC2gin0ylJOnnyZLPqP//8c/n7+zf66iw6Olrh4eGXFYB69uzp9fyaa66RpAv+smuu+++/X9u2bdOf//xn7dq1S8ePH9czzzwjf/8L/1/V559/ruuuu67R+IABA+z5lqqoqNCpU6cuuG59fb2OHj3a4nUv5siRI5o4caK6dOmi0NBQRURE6Pbbb5ckVVVVedV26NBBPXr08Bpr+Hn37t3ba/zrP/+KigpVVlZq1apVioiI8HpMmjRJklReXt7i/isqKuTxeHTDDTe0+LXN+eyBgYH6zW9+oy1btigqKkrDhw/X4sWL5Xa77XVuv/12paamauHCherWrZvuv/9+vfTSS6qpqWlxT0BrsKcKaCOcTqdiYmK0f//+Fr3uYmcILqWurq7J8YCAgCbHLctq9XtJUo8ePZScnHxZa7RFdXV1uvPOO/Xll19q3rx56t+/v0JCQvSPf/xDEydOVH19vVd9YGDgRYPmxTSs9bOf/UxpaWlN1tx4442tWrs1WvLZMzMzdd999+m1117T1q1b9atf/UqLFi3S9u3b9b3vfU9+fn565ZVX9N5772nTpk3aunWrJk+erGeeeUbvvffeBfe7AaYQqoA25N5779WqVatUVFSkpKSki9b26tVL9fX1Onz4sH3WRvrXJuXKykr16tXLHrvmmmtUWVnp9fra2lodO3as1b1eTphriV69eunQoUONxg8ePGjPt7SfiIgIderU6YLr+vv7KzY2tsW9XqiHffv26eOPP9aaNWs0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",
      "text/plain": [
       "<Figure size 640x480 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "sns.countplot(df[\"Sentiment\"])\n",
    "plt.title(\"Count Plot of target class\")\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "id": "ac6017f8",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:48.647190Z",
     "iopub.status.busy": "2024-02-04T13:04:48.646709Z",
     "iopub.status.idle": "2024-02-04T13:04:48.678563Z",
     "shell.execute_reply": "2024-02-04T13:04:48.677136Z"
    },
    "papermill": {
     "duration": 0.050361,
     "end_time": "2024-02-04T13:04:48.680823",
     "exception": false,
     "start_time": "2024-02-04T13:04:48.630462",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "497152\n",
      "Wow slept for almost 12hours. Sleepy me!! Uni now, boo! I wanna stay home, drink tea and watch house... \n"
     ]
    }
   ],
   "source": [
    "## remove stopwords and punctuation marks\n",
    "stuff_to_be_removed = list(stopwords.words('english'))+list(punctuation)\n",
    "stemmer = LancasterStemmer()\n",
    "\n",
    "corpus = df['text'].tolist()\n",
    "print(len(corpus))\n",
    "print(corpus[0])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "id": "4ce21355",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:48.712424Z",
     "iopub.status.busy": "2024-02-04T13:04:48.711356Z",
     "iopub.status.idle": "2024-02-04T13:04:49.555725Z",
     "shell.execute_reply": "2024-02-04T13:04:49.554322Z"
    },
    "papermill": {
     "duration": 0.863015,
     "end_time": "2024-02-04T13:04:49.558570",
     "exception": false,
     "start_time": "2024-02-04T13:04:48.695555",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "ename": "LookupError",
     "evalue": "\n**********************************************************************\n  Resource 'corpora/wordnet' not found.  Please use the NLTK\n  Downloader to obtain the resource:  >>> nltk.download()\n  Searched in:\n    - '/root/nltk_data'\n    - '/usr/share/nltk_data'\n    - '/usr/local/share/nltk_data'\n    - '/usr/lib/nltk_data'\n    - '/usr/local/lib/nltk_data'\n**********************************************************************",
     "output_type": "error",
     "traceback": [
      "\u001b[0;31m---------------------------------------------------------------------------\u001b[0m",
      "\u001b[0;31mLookupError\u001b[0m                               Traceback (most recent call last)",
      "File \u001b[0;32m/opt/conda/lib/python3.10/site-packages/nltk/corpus/util.py:80\u001b[0m, in \u001b[0;36mLazyCorpusLoader.__load\u001b[0;34m(self)\u001b[0m\n\u001b[1;32m     79\u001b[0m \u001b[38;5;28;01mexcept\u001b[39;00m \u001b[38;5;167;01mLookupError\u001b[39;00m \u001b[38;5;28;01mas\u001b[39;00m e:\n\u001b[0;32m---> 80\u001b[0m     \u001b[38;5;28;01mtry\u001b[39;00m: root \u001b[38;5;241m=\u001b[39m \u001b[43mnltk\u001b[49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43mdata\u001b[49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43mfind\u001b[49m\u001b[43m(\u001b[49m\u001b[38;5;124;43m'\u001b[39;49m\u001b[38;5;132;43;01m{}\u001b[39;49;00m\u001b[38;5;124;43m/\u001b[39;49m\u001b[38;5;132;43;01m{}\u001b[39;49;00m\u001b[38;5;124;43m'\u001b[39;49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43mformat\u001b[49m\u001b[43m(\u001b[49m\u001b[38;5;28;43mself\u001b[39;49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43msubdir\u001b[49m\u001b[43m,\u001b[49m\u001b[43m \u001b[49m\u001b[43mzip_name\u001b[49m\u001b[43m)\u001b[49m\u001b[43m)\u001b[49m\n\u001b[1;32m     81\u001b[0m     \u001b[38;5;28;01mexcept\u001b[39;00m \u001b[38;5;167;01mLookupError\u001b[39;00m: \u001b[38;5;28;01mraise\u001b[39;00m e\n",
      "File \u001b[0;32m/opt/conda/lib/python3.10/site-packages/nltk/data.py:653\u001b[0m, in \u001b[0;36mfind\u001b[0;34m(resource_name, paths)\u001b[0m\n\u001b[1;32m    652\u001b[0m resource_not_found \u001b[38;5;241m=\u001b[39m \u001b[38;5;124m'\u001b[39m\u001b[38;5;130;01m\\n\u001b[39;00m\u001b[38;5;132;01m%s\u001b[39;00m\u001b[38;5;130;01m\\n\u001b[39;00m\u001b[38;5;132;01m%s\u001b[39;00m\u001b[38;5;130;01m\\n\u001b[39;00m\u001b[38;5;132;01m%s\u001b[39;00m\u001b[38;5;124m'\u001b[39m \u001b[38;5;241m%\u001b[39m (sep, msg, sep)\n\u001b[0;32m--> 653\u001b[0m \u001b[38;5;28;01mraise\u001b[39;00m \u001b[38;5;167;01mLookupError\u001b[39;00m(resource_not_found)\n",
      "\u001b[0;31mLookupError\u001b[0m: \n**********************************************************************\n  Resource 'corpora/wordnet.zip/wordnet/.zip/' not found.  Please\n  use the NLTK Downloader to obtain the resource:  >>>\n  nltk.download()\n  Searched in:\n    - '/root/nltk_data'\n    - '/usr/share/nltk_data'\n    - '/usr/local/share/nltk_data'\n    - '/usr/lib/nltk_data'\n    - '/usr/local/lib/nltk_data'\n**********************************************************************",
      "\nDuring handling of the above exception, another exception occurred:\n",
      "\u001b[0;31mLookupError\u001b[0m                               Traceback (most recent call last)",
      "File \u001b[0;32m<timed exec>:19\u001b[0m\n",
      "File \u001b[0;32m<timed exec>:19\u001b[0m, in \u001b[0;36m<listcomp>\u001b[0;34m(.0)\u001b[0m\n",
      "File \u001b[0;32m/opt/conda/lib/python3.10/site-packages/nltk/stem/wordnet.py:40\u001b[0m, in \u001b[0;36mWordNetLemmatizer.lemmatize\u001b[0;34m(self, word, pos)\u001b[0m\n\u001b[1;32m     39\u001b[0m \u001b[38;5;28;01mdef\u001b[39;00m \u001b[38;5;21mlemmatize\u001b[39m(\u001b[38;5;28mself\u001b[39m, word, pos\u001b[38;5;241m=\u001b[39mNOUN):\n\u001b[0;32m---> 40\u001b[0m     lemmas \u001b[38;5;241m=\u001b[39m \u001b[43mwordnet\u001b[49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43m_morphy\u001b[49m(word, pos)\n\u001b[1;32m     41\u001b[0m     \u001b[38;5;28;01mreturn\u001b[39;00m \u001b[38;5;28mmin\u001b[39m(lemmas, key\u001b[38;5;241m=\u001b[39m\u001b[38;5;28mlen\u001b[39m) \u001b[38;5;28;01mif\u001b[39;00m lemmas \u001b[38;5;28;01melse\u001b[39;00m word\n",
      "File \u001b[0;32m/opt/conda/lib/python3.10/site-packages/nltk/corpus/util.py:116\u001b[0m, in \u001b[0;36mLazyCorpusLoader.__getattr__\u001b[0;34m(self, attr)\u001b[0m\n\u001b[1;32m    113\u001b[0m \u001b[38;5;28;01mif\u001b[39;00m attr \u001b[38;5;241m==\u001b[39m \u001b[38;5;124m'\u001b[39m\u001b[38;5;124m__bases__\u001b[39m\u001b[38;5;124m'\u001b[39m:\n\u001b[1;32m    114\u001b[0m     \u001b[38;5;28;01mraise\u001b[39;00m \u001b[38;5;167;01mAttributeError\u001b[39;00m(\u001b[38;5;124m\"\u001b[39m\u001b[38;5;124mLazyCorpusLoader object has no attribute \u001b[39m\u001b[38;5;124m'\u001b[39m\u001b[38;5;124m__bases__\u001b[39m\u001b[38;5;124m'\u001b[39m\u001b[38;5;124m\"\u001b[39m)\n\u001b[0;32m--> 116\u001b[0m \u001b[38;5;28;43mself\u001b[39;49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43m__load\u001b[49m\u001b[43m(\u001b[49m\u001b[43m)\u001b[49m\n\u001b[1;32m    117\u001b[0m \u001b[38;5;66;03m# This looks circular, but its not, since __load() changes our\u001b[39;00m\n\u001b[1;32m    118\u001b[0m \u001b[38;5;66;03m# __class__ to something new:\u001b[39;00m\n\u001b[1;32m    119\u001b[0m \u001b[38;5;28;01mreturn\u001b[39;00m \u001b[38;5;28mgetattr\u001b[39m(\u001b[38;5;28mself\u001b[39m, attr)\n",
      "File \u001b[0;32m/opt/conda/lib/python3.10/site-packages/nltk/corpus/util.py:81\u001b[0m, in \u001b[0;36mLazyCorpusLoader.__load\u001b[0;34m(self)\u001b[0m\n\u001b[1;32m     79\u001b[0m     \u001b[38;5;28;01mexcept\u001b[39;00m \u001b[38;5;167;01mLookupError\u001b[39;00m \u001b[38;5;28;01mas\u001b[39;00m e:\n\u001b[1;32m     80\u001b[0m         \u001b[38;5;28;01mtry\u001b[39;00m: root \u001b[38;5;241m=\u001b[39m nltk\u001b[38;5;241m.\u001b[39mdata\u001b[38;5;241m.\u001b[39mfind(\u001b[38;5;124m'\u001b[39m\u001b[38;5;132;01m{}\u001b[39;00m\u001b[38;5;124m/\u001b[39m\u001b[38;5;132;01m{}\u001b[39;00m\u001b[38;5;124m'\u001b[39m\u001b[38;5;241m.\u001b[39mformat(\u001b[38;5;28mself\u001b[39m\u001b[38;5;241m.\u001b[39msubdir, zip_name))\n\u001b[0;32m---> 81\u001b[0m         \u001b[38;5;28;01mexcept\u001b[39;00m \u001b[38;5;167;01mLookupError\u001b[39;00m: \u001b[38;5;28;01mraise\u001b[39;00m e\n\u001b[1;32m     83\u001b[0m \u001b[38;5;66;03m# Load the corpus.\u001b[39;00m\n\u001b[1;32m     84\u001b[0m corpus \u001b[38;5;241m=\u001b[39m \u001b[38;5;28mself\u001b[39m\u001b[38;5;241m.\u001b[39m__reader_cls(root, \u001b[38;5;241m*\u001b[39m\u001b[38;5;28mself\u001b[39m\u001b[38;5;241m.\u001b[39m__args, \u001b[38;5;241m*\u001b[39m\u001b[38;5;241m*\u001b[39m\u001b[38;5;28mself\u001b[39m\u001b[38;5;241m.\u001b[39m__kwargs)\n",
      "File \u001b[0;32m/opt/conda/lib/python3.10/site-packages/nltk/corpus/util.py:78\u001b[0m, in \u001b[0;36mLazyCorpusLoader.__load\u001b[0;34m(self)\u001b[0m\n\u001b[1;32m     76\u001b[0m \u001b[38;5;28;01melse\u001b[39;00m:\n\u001b[1;32m     77\u001b[0m     \u001b[38;5;28;01mtry\u001b[39;00m:\n\u001b[0;32m---> 78\u001b[0m         root \u001b[38;5;241m=\u001b[39m \u001b[43mnltk\u001b[49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43mdata\u001b[49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43mfind\u001b[49m\u001b[43m(\u001b[49m\u001b[38;5;124;43m'\u001b[39;49m\u001b[38;5;132;43;01m{}\u001b[39;49;00m\u001b[38;5;124;43m/\u001b[39;49m\u001b[38;5;132;43;01m{}\u001b[39;49;00m\u001b[38;5;124;43m'\u001b[39;49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43mformat\u001b[49m\u001b[43m(\u001b[49m\u001b[38;5;28;43mself\u001b[39;49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43msubdir\u001b[49m\u001b[43m,\u001b[49m\u001b[43m \u001b[49m\u001b[38;5;28;43mself\u001b[39;49m\u001b[38;5;241;43m.\u001b[39;49m\u001b[43m__name\u001b[49m\u001b[43m)\u001b[49m\u001b[43m)\u001b[49m\n\u001b[1;32m     79\u001b[0m     \u001b[38;5;28;01mexcept\u001b[39;00m \u001b[38;5;167;01mLookupError\u001b[39;00m \u001b[38;5;28;01mas\u001b[39;00m e:\n\u001b[1;32m     80\u001b[0m         \u001b[38;5;28;01mtry\u001b[39;00m: root \u001b[38;5;241m=\u001b[39m nltk\u001b[38;5;241m.\u001b[39mdata\u001b[38;5;241m.\u001b[39mfind(\u001b[38;5;124m'\u001b[39m\u001b[38;5;132;01m{}\u001b[39;00m\u001b[38;5;124m/\u001b[39m\u001b[38;5;132;01m{}\u001b[39;00m\u001b[38;5;124m'\u001b[39m\u001b[38;5;241m.\u001b[39mformat(\u001b[38;5;28mself\u001b[39m\u001b[38;5;241m.\u001b[39msubdir, zip_name))\n",
      "File \u001b[0;32m/opt/conda/lib/python3.10/site-packages/nltk/data.py:653\u001b[0m, in \u001b[0;36mfind\u001b[0;34m(resource_name, paths)\u001b[0m\n\u001b[1;32m    651\u001b[0m sep \u001b[38;5;241m=\u001b[39m \u001b[38;5;124m'\u001b[39m\u001b[38;5;124m*\u001b[39m\u001b[38;5;124m'\u001b[39m \u001b[38;5;241m*\u001b[39m \u001b[38;5;241m70\u001b[39m\n\u001b[1;32m    652\u001b[0m resource_not_found \u001b[38;5;241m=\u001b[39m \u001b[38;5;124m'\u001b[39m\u001b[38;5;130;01m\\n\u001b[39;00m\u001b[38;5;132;01m%s\u001b[39;00m\u001b[38;5;130;01m\\n\u001b[39;00m\u001b[38;5;132;01m%s\u001b[39;00m\u001b[38;5;130;01m\\n\u001b[39;00m\u001b[38;5;132;01m%s\u001b[39;00m\u001b[38;5;124m'\u001b[39m \u001b[38;5;241m%\u001b[39m (sep, msg, sep)\n\u001b[0;32m--> 653\u001b[0m \u001b[38;5;28;01mraise\u001b[39;00m \u001b[38;5;167;01mLookupError\u001b[39;00m(resource_not_found)\n",
      "\u001b[0;31mLookupError\u001b[0m: \n**********************************************************************\n  Resource 'corpora/wordnet' not found.  Please use the NLTK\n  Downloader to obtain the resource:  >>> nltk.download()\n  Searched in:\n    - '/root/nltk_data'\n    - '/usr/share/nltk_data'\n    - '/usr/local/share/nltk_data'\n    - '/usr/lib/nltk_data'\n    - '/usr/local/lib/nltk_data'\n**********************************************************************"
     ]
    }
   ],
   "source": [
    "%%time\n",
    "final_corpus = []\n",
    "final_corpus_joined = []\n",
    "for i in df.index:\n",
    "\n",
    "    text = re.sub('[^a-zA-Z]', ' ', df['text'][i])\n",
    "    #Convert to lowercase\n",
    "    text = text.lower()\n",
    "    #remove tags\n",
    "    text=re.sub(\"&lt;/?.*?&gt;\",\" &lt;&gt; \",text)\n",
    "    \n",
    "    # remove special characters and digits\n",
    "    text=re.sub(\"(\\\\d|\\\\W)+\",\" \",text)\n",
    "    \n",
    "    ##Convert to list from string\n",
    "    text = text.split()\n",
    "\n",
    "    #Lemmatisation\n",
    "    lem = WordNetLemmatizer()\n",
    "    text = [lem.lemmatize(word) for word in text \n",
    "            if not word in stuff_to_be_removed] \n",
    "    text1 = \" \".join(text)\n",
    "    final_corpus.append(text)\n",
    "    final_corpus_joined.append(text1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "id": "f9741ccf",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:49.590727Z",
     "iopub.status.busy": "2024-02-04T13:04:49.590333Z",
     "iopub.status.idle": "2024-02-04T13:04:49.613153Z",
     "shell.execute_reply": "2024-02-04T13:04:49.612027Z"
    },
    "papermill": {
     "duration": 0.042386,
     "end_time": "2024-02-04T13:04:49.615956",
     "exception": false,
     "start_time": "2024-02-04T13:04:49.573570",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "data_cleaned = pd.DataFrame()\n",
    "data_cleaned[\"text\"] = final_corpus_joined\n",
    "data_cleaned[\"Sentiment\"] = df[\"Sentiment\"].values"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "id": "1d2fb095",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:49.647583Z",
     "iopub.status.busy": "2024-02-04T13:04:49.647147Z",
     "iopub.status.idle": "2024-02-04T13:04:49.661196Z",
     "shell.execute_reply": "2024-02-04T13:04:49.660126Z"
    },
    "papermill": {
     "duration": 0.032662,
     "end_time": "2024-02-04T13:04:49.663526",
     "exception": false,
     "start_time": "2024-02-04T13:04:49.630864",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "Sentiment\n",
       "0    248576\n",
       "1    248576\n",
       "Name: count, dtype: int64"
      ]
     },
     "execution_count": 19,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "data_cleaned['Sentiment'].value_counts()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "id": "d6a7233e",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:49.694937Z",
     "iopub.status.busy": "2024-02-04T13:04:49.694511Z",
     "iopub.status.idle": "2024-02-04T13:04:49.705630Z",
     "shell.execute_reply": "2024-02-04T13:04:49.704415Z"
    },
    "papermill": {
     "duration": 0.029423,
     "end_time": "2024-02-04T13:04:49.707895",
     "exception": false,
     "start_time": "2024-02-04T13:04:49.678472",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>text</th>\n",
       "      <th>Sentiment</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   text  Sentiment\n",
       "0   NaN          0\n",
       "1   NaN          0\n",
       "2   NaN          0\n",
       "3   NaN          0\n",
       "4   NaN          0"
      ]
     },
     "execution_count": 20,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "data_cleaned.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "id": "ea71d003",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:49.739756Z",
     "iopub.status.busy": "2024-02-04T13:04:49.739342Z",
     "iopub.status.idle": "2024-02-04T13:04:49.774347Z",
     "shell.execute_reply": "2024-02-04T13:04:49.773220Z"
    },
    "papermill": {
     "duration": 0.053704,
     "end_time": "2024-02-04T13:04:49.776647",
     "exception": false,
     "start_time": "2024-02-04T13:04:49.722943",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>text</th>\n",
       "      <th>Sentiment</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   text  Sentiment\n",
       "0   NaN          0\n",
       "1   NaN          0\n",
       "2   NaN          0\n",
       "3   NaN          0\n",
       "4   NaN          0"
      ]
     },
     "execution_count": 21,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#Exploratory Data Analysis (EDA)\n",
    "data_eda = pd.DataFrame()\n",
    "data_eda['text'] = final_corpus\n",
    "data_eda['Sentiment'] = df[\"Sentiment\"].values\n",
    "data_eda.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 22,
   "id": "e48a731b",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:49.810502Z",
     "iopub.status.busy": "2024-02-04T13:04:49.809230Z",
     "iopub.status.idle": "2024-02-04T13:04:49.867526Z",
     "shell.execute_reply": "2024-02-04T13:04:49.866145Z"
    },
    "papermill": {
     "duration": 0.077852,
     "end_time": "2024-02-04T13:04:49.870333",
     "exception": false,
     "start_time": "2024-02-04T13:04:49.792481",
     "status": "completed"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "# Storing positive data seperately\n",
    "positive = data_eda[data_eda['Sentiment'] == 1]\n",
    "positive_list = positive['text'].tolist()\n",
    "\n",
    "# Storing negative data seperately\n",
    "\n",
    "negative = data_eda[data_eda['Sentiment'] == 0]\n",
    "negative_list = negative['text'].tolist()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 23,
   "id": "c8fed7e4",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:04:49.903967Z",
     "iopub.status.busy": "2024-02-04T13:04:49.902782Z",
     "iopub.status.idle": "2024-02-04T13:04:50.154277Z",
     "shell.execute_reply": "2024-02-04T13:04:50.152467Z"
    },
    "papermill": {
     "duration": 0.270818,
     "end_time": "2024-02-04T13:04:50.156609",
     "exception": true,
     "start_time": "2024-02-04T13:04:49.885791",
     "status": "failed"
    },
    "tags": []
   },
   "outputs": [
    {
     "ename": "TypeError",
     "evalue": "'float' object is not iterable",
     "output_type": "error",
     "traceback": [
      "\u001b[0;31m---------------------------------------------------------------------------\u001b[0m",
      "\u001b[0;31mTypeError\u001b[0m                                 Traceback (most recent call last)",
      "Cell \u001b[0;32mIn[23], line 1\u001b[0m\n\u001b[0;32m----> 1\u001b[0m positive_all \u001b[38;5;241m=\u001b[39m \u001b[38;5;124m\"\u001b[39m\u001b[38;5;124m \u001b[39m\u001b[38;5;124m\"\u001b[39m\u001b[38;5;241m.\u001b[39mjoin([word \u001b[38;5;28;01mfor\u001b[39;00m sent \u001b[38;5;129;01min\u001b[39;00m positive_list \u001b[38;5;28;01mfor\u001b[39;00m word \u001b[38;5;129;01min\u001b[39;00m sent ])\n\u001b[1;32m      2\u001b[0m negative_all \u001b[38;5;241m=\u001b[39m \u001b[38;5;124m\"\u001b[39m\u001b[38;5;124m \u001b[39m\u001b[38;5;124m\"\u001b[39m\u001b[38;5;241m.\u001b[39mjoin([word \u001b[38;5;28;01mfor\u001b[39;00m sent \u001b[38;5;129;01min\u001b[39;00m negative_list \u001b[38;5;28;01mfor\u001b[39;00m word \u001b[38;5;129;01min\u001b[39;00m sent ])\n",
      "Cell \u001b[0;32mIn[23], line 1\u001b[0m, in \u001b[0;36m<listcomp>\u001b[0;34m(.0)\u001b[0m\n\u001b[0;32m----> 1\u001b[0m positive_all \u001b[38;5;241m=\u001b[39m \u001b[38;5;124m\"\u001b[39m\u001b[38;5;124m \u001b[39m\u001b[38;5;124m\"\u001b[39m\u001b[38;5;241m.\u001b[39mjoin([word \u001b[38;5;28;01mfor\u001b[39;00m sent \u001b[38;5;129;01min\u001b[39;00m positive_list \u001b[38;5;28;01mfor\u001b[39;00m word \u001b[38;5;129;01min\u001b[39;00m sent ])\n\u001b[1;32m      2\u001b[0m negative_all \u001b[38;5;241m=\u001b[39m \u001b[38;5;124m\"\u001b[39m\u001b[38;5;124m \u001b[39m\u001b[38;5;124m\"\u001b[39m\u001b[38;5;241m.\u001b[39mjoin([word \u001b[38;5;28;01mfor\u001b[39;00m sent \u001b[38;5;129;01min\u001b[39;00m negative_list \u001b[38;5;28;01mfor\u001b[39;00m word \u001b[38;5;129;01min\u001b[39;00m sent ])\n",
      "\u001b[0;31mTypeError\u001b[0m: 'float' object is not iterable"
     ]
    }
   ],
   "source": [
    "positive_all = \" \".join([word for sent in positive_list for word in sent ])\n",
    "negative_all = \" \".join([word for sent in negative_list for word in sent ])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "69fb8ecc",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "#Worldcloud for positive data\n",
    "from wordcloud import WordCloud\n",
    "WordCloud()\n",
    "wordcloud = WordCloud(width=2000,\n",
    "                      height=1000,\n",
    "                      background_color='#F2EDD7FF',\n",
    "                      max_words = 100).generate(positive_all)\n",
    "\n",
    "plt.figure(figsize=(20,30))\n",
    "plt.imshow(wordcloud)\n",
    "plt.title(\"Positive\")\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "74265c61",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "#Worldcloud for negative data\n",
    "from wordcloud import WordCloud\n",
    "WordCloud()\n",
    "wordcloud = WordCloud(width=2000,\n",
    "                      height=1000,\n",
    "                      background_color='#F2EDD7FF',\n",
    "                      max_words = 100).generate(negative_all)\n",
    "\n",
    "plt.figure(figsize=(20,30))\n",
    "plt.imshow(wordcloud)\n",
    "plt.title(\"negative\")\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "1e8f17db",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "def get_count(data):\n",
    "    dic = {}\n",
    "    for i in data:\n",
    "        for j in i:\n",
    "            if j not in dic:\n",
    "                dic[j]=1\n",
    "            else:\n",
    "                dic[j]+=1    \n",
    "            \n",
    "    return(dic)\n",
    "count_corpus = get_count(positive_list)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "67379a4b",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "count_corpus = pd.DataFrame({\"word\":count_corpus.keys(),\"count\":count_corpus.values()})\n",
    "count_corpus = count_corpus.sort_values(by = \"count\", ascending = False)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "7158bacc",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "import seaborn as sns\n",
    "plt.figure(figsize = (15,10))\n",
    "sns.barplot(x = count_corpus[\"word\"][:20], y = count_corpus[\"count\"][:20])\n",
    "plt.title('one words in positive data')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "94a58af5",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "def get_count(data):\n",
    "    dic = {}\n",
    "    for i in data:\n",
    "        for j in i:\n",
    "            if j not in dic:\n",
    "                dic[j]=1\n",
    "            else:\n",
    "                dic[j]+=1    \n",
    "            \n",
    "    #dic = dict(sorted(dic.items() , key = lambda x:x[1],reverse=True))\n",
    "    return(dic)\n",
    "count_corpus = get_count(negative_list)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "955ba009",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "count_corpus = pd.DataFrame({\"word\":count_corpus.keys(),\"count\":count_corpus.values()})\n",
    "count_corpus = count_corpus.sort_values(by = \"count\", ascending = False)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "eb988e69",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "import seaborn as sns\n",
    "plt.figure(figsize = (15,10))\n",
    "sns.barplot(x = count_corpus[\"word\"][:20], y = count_corpus[\"count\"][:20])\n",
    "plt.title('one words in negative data')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "6348dbe5",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "#Classification\n",
    "\n",
    "#Naive bayes for Sentiment Analysis\n",
    "def get_tweets_for_model(cleaned_tokens_list):\n",
    "    for tweet_tokens in cleaned_tokens_list:\n",
    "        yield dict([token, True] for token in tweet_tokens)\n",
    "\n",
    "positive_tokens_for_model = get_tweets_for_model(positive_list)\n",
    "negative_tokens_for_model = get_tweets_for_model(negative_list)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "04d68d76",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "import random\n",
    "\n",
    "positive_dataset = [(review_dict, \"Positive\")\n",
    "                     for review_dict in positive_tokens_for_model]\n",
    "\n",
    "negative_dataset = [(review_dict, \"Negative\")\n",
    "                     for review_dict in negative_tokens_for_model]\n",
    "dataset = positive_dataset + negative_dataset\n",
    "\n",
    "random.shuffle(dataset)\n",
    "\n",
    "train_data = dataset[:333091]\n",
    "test_data = dataset[333091:]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "371fef6c",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "from nltk import classify\n",
    "from nltk import NaiveBayesClassifier\n",
    "classifier = NaiveBayesClassifier.train(train_data)\n",
    "\n",
    "print(\" Training Accuracy is:\", round(classify.accuracy(classifier, train_data),2)*100)\n",
    "\n",
    "print(\"Testing Accuracy is:\", round(classify.accuracy(classifier, test_data),2)*100)\n",
    "\n",
    "print(classifier.show_most_informative_features(10))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "0bc4c00b",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "#TFIDF for Sentiment Analysis\n",
    "from sklearn.feature_extraction.text import TfidfVectorizer\n",
    "tfidf = TfidfVectorizer()\n",
    "vector = tfidf.fit_transform(data_cleaned['text'])\n",
    "y = data_cleaned['Sentiment']"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "d8104b6a",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "from sklearn.model_selection import train_test_split\n",
    "X_train, X_test, y_train, y_test = train_test_split(vector, \n",
    "                                                    y, \n",
    "                                                    test_size=0.33, \n",
    "                                                    random_state=42,\n",
    "                                                    stratify = y)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e51ac693",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "from sklearn.naive_bayes import MultinomialNB\n",
    "from sklearn.svm import LinearSVC\n",
    "from sklearn.metrics import ConfusionMatrixDisplay\n",
    "from sklearn.discriminant_analysis import LinearDiscriminantAnalysis\n",
    "from sklearn.metrics import accuracy_score,confusion_matrix,classification_report\n",
    "from sklearn.linear_model import LogisticRegression"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "c506612a",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "def metrics(y_train,y_train_pred,y_test,y_test_pred):\n",
    "  print(\"training accuracy = \",round(accuracy_score(y_train,y_train_pred),2)*100)\n",
    "  ConfusionMatrixDisplay.from_predictions(y_train,y_train_pred,normalize = 'all')\n",
    "  print(classification_report(y_train,y_train_pred))\n",
    "  plt.show()\n",
    "\n",
    "  print(\"testing accuracy = \",round(accuracy_score(y_test,y_test_pred),2)*100)\n",
    "  ConfusionMatrixDisplay.from_predictions(y_test,y_test_pred,normalize = 'all')\n",
    "  print(classification_report(y_test,y_test_pred))\n",
    "  plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "670a8360",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:01:06.327087Z",
     "iopub.status.busy": "2024-02-04T13:01:06.325995Z",
     "iopub.status.idle": "2024-02-04T13:01:06.357302Z",
     "shell.execute_reply": "2024-02-04T13:01:06.355762Z",
     "shell.execute_reply.started": "2024-02-04T13:01:06.327049Z"
    },
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "#Multinomial NB\n",
    "NB = MultinomialNB()\n",
    "NB.fit(X_train,y_train)\n",
    "y_train_pred = NB.predict(X_train)\n",
    "y_test_pred = NB.predict(X_test)\n",
    "metrics(y_train,y_train_pred,y_test,y_test_pred)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "cfbcec74",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:01:02.174952Z",
     "iopub.status.busy": "2024-02-04T13:01:02.174550Z",
     "iopub.status.idle": "2024-02-04T13:01:02.205320Z",
     "shell.execute_reply": "2024-02-04T13:01:02.204179Z",
     "shell.execute_reply.started": "2024-02-04T13:01:02.174920Z"
    },
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "#Linear SVC\n",
    "svc = LinearSVC()\n",
    "svc.fit(X_train,y_train)\n",
    "y_train_pred = svc.predict(X_train)\n",
    "y_test_pred = svc.predict(X_test)\n",
    "metrics(y_train,y_train_pred,y_test,y_test_pred)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9ceae63d",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:00:16.841305Z",
     "iopub.status.busy": "2024-02-04T13:00:16.840816Z",
     "iopub.status.idle": "2024-02-04T13:00:17.346605Z",
     "shell.execute_reply": "2024-02-04T13:00:17.344666Z",
     "shell.execute_reply.started": "2024-02-04T13:00:16.841252Z"
    },
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "#Logic Regression\n",
    "lr = LogisticRegression()\n",
    "lr.fit(X_train,y_train)\n",
    "y_train_pred = lr.predict(X_train)\n",
    "y_test_pred = lr.predict(X_test)\n",
    "metrics(y_train,y_train_pred,y_test,y_test_pred)"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "17ac73d7",
   "metadata": {
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "4d28b231",
   "metadata": {
    "execution": {
     "iopub.execute_input": "2024-02-04T13:03:27.758573Z",
     "iopub.status.busy": "2024-02-04T13:03:27.758132Z",
     "iopub.status.idle": "2024-02-04T13:03:28.227253Z",
     "shell.execute_reply": "2024-02-04T13:03:28.226013Z",
     "shell.execute_reply.started": "2024-02-04T13:03:27.758533Z"
    },
    "papermill": {
     "duration": null,
     "end_time": null,
     "exception": null,
     "start_time": null,
     "status": "pending"
    },
    "tags": []
   },
   "outputs": [],
   "source": [
    "import pandas as pd\n",
    "\n",
    "# Create a DataFrame with the provided information\n",
    "data = {\n",
    "    'Model': ['Naive Bayes', 'Multinomial NB', 'Linear SVC', 'Logistic'],\n",
    "    'Training Accuracy': [86, 85, 90, 83],\n",
    "    'Testing Accuracy': [76, 76, 77, 78]\n",
    "}\n",
    "\n",
    "df = pd.DataFrame(data)\n",
    "\n",
    "# Display the DataFrame\n",
    "print(df)"
   ]
  }
 ],
 "metadata": {
  "kaggle": {
   "accelerator": "none",
   "dataSources": [
    {
     "datasetId": 989445,
     "sourceId": 1808590,
     "sourceType": "datasetVersion"
    }
   ],
   "dockerImageVersionId": 30646,
   "isGpuEnabled": false,
   "isInternetEnabled": false,
   "language": "python",
   "sourceType": "notebook"
  },
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.10.13"
  },
  "papermill": {
   "default_parameters": {},
   "duration": 16.033668,
   "end_time": "2024-02-04T13:04:51.195933",
   "environment_variables": {},
   "exception": true,
   "input_path": "__notebook__.ipynb",
   "output_path": "__notebook__.ipynb",
   "parameters": {},
   "start_time": "2024-02-04T13:04:35.162265",
   "version": "2.5.0"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
