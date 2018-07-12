{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
   
    "\n",
    "<i> Adapted for Chalab by Isabelle Guyon from original code of Balázs Kégl (LAL/CNRS)</i>\n",
    "\n",
    "ALL INFORMATION, SOFTWARE, DOCUMENTATION, AND DATA ARE PROVIDED \"AS-IS\". The CDS, CHALEARN, AND/OR OTHER ORGANIZERS OR CODE AUTHORS DISCLAIM ANY EXPRESSED OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR ANY PARTICULAR PURPOSE, AND THE WARRANTY OF NON-INFRIGEMENT OF ANY THIRD PARTY'S INTELLECTUAL PROPERTY RIGHTS. IN NO EVENT SHALL AUTHORS AND ORGANIZERS BE LIABLE FOR ANY SPECIAL, \n",
    "INDIRECT OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF SOFTWARE, DOCUMENTS, MATERIALS, PUBLICATIONS, OR INFORMATION MADE AVAILABLE FOR THE CHALLENGE. "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Fetch the data and load it"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Introduction\n",
    "\n",
    "The task of HIVA is to predict which compounds are active against the AIDS HIV infection. The dataset have two classes : active or inactive ( Binary Classification). The variables represent properties of the molecule inferred from its structure.\n",
    "\n",
    "The problem is therefore to relate structure to activity (a QSAR=quantitative structure - activity relationship problem) to screen new compounds before actually testing them (a HTS=high - throughput screening problem)"
   ]
  }
  {
   "cell_type": "code",
   "execution_count": 31,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "codedir = 'sample_code/'                        # Change this to the directory where you put the code\n",
    "from sys import path; path.append(codedir)\n",
    "%matplotlib inline\n",
    "import seaborn as sns; sns.set()\n",
    "\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 32,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "datadir = 'public_data/'                        # Change this to the directory where you put the input data\n",
    "dataname = 'hiva'\n",
    "basename = datadir  + dataname\n",
    "# !ls $basename*"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 33,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Reading public_data/hiva_train from AutoML format\n",
      "Number of examples = 3845\n",
      "Number of features = 1617\n",
      "Number of classes = 2\n"
     ]
    },
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0xaa984fac>"
      ]
     },
     "execution_count": 33,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAfkAAAFmCAYAAABuhuNyAAAABHNCSVQICAgIfAhkiAAAAAlwSFlz\nAAALEgAACxIB0t1+/AAAIABJREFUeJzt3W1wVOXdx/HfPiSryUKSpdkogtqbCqktaxOsPMkgDKaY\nGR+wYcRotA2VUdpomQQbkozi3SgBQ6OVph0fqERRVOK00dGkrbFMGyIgSanQsTqhdqSpm90klCwJ\nwpK9XzjuLVZC1D1ZcvH9vErO7jn7X8b1u9c5sGuLRCIRAQAA49jjPQAAALAGkQcAwFBEHgAAQxF5\nAAAMReQBADAUkQcAwFCWR/7DDz/UlVdeqd/85jf64IMPVFBQoJtvvlkrVqzQsWPHJEkNDQ3Ky8vT\nDTfcoK1bt0qSwuGwSkpKlJ+fr4KCAh04cMDqUQEAMIrlka+trVVqaqok6eGHH1ZBQYGefvppnX/+\n+aqvr9fAwIBqa2u1adMm1dXVadOmTTp06JBefvllpaSk6JlnntHtt9+u9evXWz0qAABGsTTy+/fv\n1/79+zV37lxFIhHt2rVL8+bNkyTNmzdP27dv1549e+Tz+ZScnCyXy6Xs7Gzt3r1bra2tWrBggSRp\n1qxZamtrs3JUAACMY2nk165dq9LS0ujvAwMDSkhIkCSNGzdOXV1d6u7ulsfjid7H4/EoEAgoGAxG\nt9tsNtntdoXDYSvHBQDAKJZF/je/+Y2ysrJ03nnnfebtJ/s03ZNtHxwcjNlsAACcCZxWHXjbtm06\ncOCAXn/9dfn9fiUkJCgpKUlHjx5VYmKi/H6/MjIy5PV6FQgEovv5/X5lZWXJ6/UqGAxqypQp0RW8\n03nqccPh43I6HVY9LQAARg3LIl9TUxP9ecOGDZowYYLa2trU2Nioa665Rk1NTZozZ458Pp8qKioU\nCoVks9nU3t6u8vJy9fX1qbGxUbNnz1Zzc7OmT58+rMft7e236ikBAHDaSU8fc9LbLIv8Z7nzzjt1\n99136/nnn9f48eO1aNEiORwOFRcXq7CwUHa7XUVFRXK73crNzVVLS4vy8/PlcrlUVVU1kqMCADDq\n2Uz7qtlAoC/eIwAAMGKGWsnziXcAABiKyAMAYCgiDwCAoYg8AACGIvIAABiKyAMAYCgiDwCAoYg8\nAACGIvIAABiKyAMAYCgiDwCAoYg8AACGGtFvoRutjh8/rvfe2x/vMYCYuPDC/5HD4Yj3GABGAJEf\nhvfe26/bqpfrrLFJ8R4F+FKOHOrXYyW1mjTponiPAmAEEPlhOmtskpI87niPAQDAsHFNHgAAQxF5\nAAAMReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBE\nHgAAQxF5AAAMReQBADCUpd8nf+TIEZWWlqq7u1tHjx7VHXfcoaamJu3du1dpaWmSpKVLl2ru3Llq\naGhQXV2dHA6HFi9erLy8PIXDYZWWlqqzs1MOh0Nr1qzRhAkTrBwZAABjWBr55uZmTZ06VUuXLlVn\nZ6e+//3vKzs7WyUlJZo7d270fgMDA6qtrVV9fb2cTqfy8vKUk5Oj5uZmpaSkqLq6Wi0tLVq/fr1q\namqsHBkAAGNYGvnc3Nzoz52dnTr33HMlSZFI5IT77dmzRz6fT8nJyZKk7Oxs7d69W62trbruuusk\nSbNmzVJZWZmV4wIAYJQRuSa/ZMkS3X333dFIb968WbfeequKi4vV29urYDAoj8cTvb/H41EgEDhh\nu81mk91uVzgcHomRAQAY9SxdyX9sy5Ytevvtt1VSUqKysjKlpqYqMzNTjz32mDZs2KCsrKwT7v/p\nlf7HBgcHT/lYaWlJcjodMZn7Y7297pgeD4gnj8et9PQx8R4DwAiwNPL79u3TuHHjdM455ygzM1PH\njx/X5MmTo6vz+fPna/Xq1Vq4cKFef/316H5+v19ZWVnyer0KBoOaMmVKdAXvdA49cm9vf8yfR09P\nKObHBOKlpyekQKAv3mMAiJGh3rRberp+165d2rhxoyQpGAyqv79f9957r95//31J0o4dOzR58mT5\nfD7t3btXoVBIhw8fVnt7u6ZNm6bZs2ersbFR0kd/iW/69OlWjgsAgFEsXcnfeOONKisr00033aQP\nP/xQ9957r5KSkrRixQqdffbZSk5O1gMPPCCXy6Xi4mIVFhbKbrerqKhIbrdbubm5amlpUX5+vlwu\nl6qqqqwcFwAAo9giJ7sAPkpZcRqyo+NdFT1aoiQP1+YxuvX3hPTIsmpNmnRRvEcBECNxO10PAADi\nh8gDAGAoIg8AgKGIPAAAhiLyAAAYisgDAGAoIg8AgKGIPAAAhiLyAAAYisgDAGAoIg8AgKGIPAAA\nhiLyAAAYisgDAGAoIg8AgKGIPAAAhiLyAAAYisgDAGAoIg8AgKGIPAAAhiLyAAAYisgDAGAoIg8A\ngKGIPAAAhiLyAAAYisgDAGAoIg8AgKGIPAAAhiLyAAAYymnlwY8cOaLS0lJ1d3fr6NGjuuOOO5SZ\nmamVK1cqEokoPT1d69atU0JCghoaGlRXVyeHw6HFixcrLy9P4XBYpaWl6uzslMPh0Jo1azRhwgQr\nRwYAwBiWRr65uVlTp07V0qVL1dnZqe9///vKzs7WzTffrO985zuqqalRfX29rr32WtXW1qq+vl5O\np1N5eXnKyclRc3OzUlJSVF1drZaWFq1fv141NTVWjgwAgDEsPV2fm5urpUuXSpI6Ozt17rnnateu\nXZo/f74kad68edq+fbv27Nkjn8+n5ORkuVwuZWdna/fu3WptbdWCBQskSbNmzVJbW5uV4wIAYBRL\nV/IfW7Jkibq6uvTLX/5ShYWFSkhIkCSNGzdOXV1d6u7ulsfjid7f4/EoEAgoGAxGt9tsNtntdoXD\nYTmdIzI2AACj2ojUcsuWLXr77bdVUlKiSCQS3f7Jnz/pZNsHBwctmQ8AABNZGvl9+/Zp3LhxOuec\nc5SZmanBwUElJyfr6NGjSkxMlN/vV0ZGhrxerwKBQHQ/v9+vrKwseb1eBYNBTZkyReFw+KOBT7GK\nT0tLktPpiOnz6O11x/R4QDx5PG6lp4+J9xgARoClkd+1a5c6OztVVlamYDCo/v5+zZkzR42Njbrm\nmmvU1NSkOXPmyOfzqaKiQqFQSDabTe3t7SovL1dfX58aGxs1e/ZsNTc3a/r06ad8zN7e/pg/j56e\nUMyPCcRLT09IgUBfvMcAECNDvWm3NPI33nijysrKdNNNN+nDDz/U6tWr9Y1vfEN33323nn/+eY0f\nP16LFi2Sw+FQcXGxCgsLZbfbVVRUJLfbrdzcXLW0tCg/P18ul0tVVVVWjgsAgFFskZNdAB+lrFih\ndHS8q6JHS5Tk4bQ9Rrf+npAeWVatSZMuivcoAGJkqJU8n3gHAIChiDwAAIYi8gAAGIrIAwBgKCIP\nAIChiDwAAIYi8gAAGIrIAwBgKCIPAIChiDwAAIYi8gAAGIrIAwBgKCIPAIChiDwAAIYi8gAAGIrI\nAwBgKCIPAIChiDwAAIYi8gAAGIrIAwBgKCIPAIChiDwAAIYi8gAAGIrIAwBgKCIPAIChiDwAAIYi\n8gAAGIrIAwBgKCIPAIChnFY/wLp169TW1qbjx49r2bJlam5u1t69e5WWliZJWrp0qebOnauGhgbV\n1dXJ4XBo8eLFysvLUzgcVmlpqTo7O+VwOLRmzRpNmDDB6pEBADCCpZHfsWOHOjo6tGXLFh08eFCL\nFi3SjBkzVFJSorlz50bvNzAwoNraWtXX18vpdCovL085OTlqbm5WSkqKqqur1dLSovXr16umpsbK\nkQEAMIalp+svu+wyPfzww5KksWPHqr+/X4ODg4pEIifcb8+ePfL5fEpOTpbL5VJ2drZ2796t1tZW\nLViwQJI0a9YstbW1WTkuAABGsTTyNptNZ511liTphRde0BVXXCG73a6nn35at956q4qLi9Xb26tg\nMCiPxxPdz+PxKBAInLDdZrPJbrcrHA5bOTIAAMaw/Jq8JP3hD3/Qiy++qCeeeEJ79+5VamqqMjMz\n9dhjj2nDhg3Kyso64f6fXul/bHBwcCTGBQDACJZH/k9/+pMeffRRPfHEE3K73ZoxY0b0tvnz52v1\n6tVauHChXn/99eh2v9+vrKwseb1eBYNBTZkyJbqCdzqHHjktLUlOpyOmz6G31x3T4wHx5PG4lZ4+\nJt5jABgBlkY+FArpwQcf1JNPPqkxYz76n8qdd96plStXauLEidqxY4cmT54sn8+niooKhUIh2Ww2\ntbe3q7y8XH19fWpsbNTs2bPV3Nys6dOnn/Ixe3v7Y/48enpCMT8mEC89PSEFAn3xHgNAjAz1pt3S\nyL/yyis6ePCgfvzjHysSichms+n666/XihUrdPbZZys5OVkPPPCAXC6XiouLVVhYKLvdrqKiIrnd\nbuXm5qqlpUX5+flyuVyqqqqyclwAAIxii5zsAvgoZcUKpaPjXRU9WqIkD6ftMbr194T0yLJqTZp0\nUbxHARAjQ63k+cQ7AAAMReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBEHgAAQxF5AAAM\nReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBEHgAA\nQxF5AAAMReQBADAUkQcAwFDDinxpael/bVu6dGnMhwEAALHjHOrGhoYGbdmyRe+++65uuumm6PZj\nx44pGAxaPhwAAPjihoz8Nddco+nTp6ukpERFRUXR7Xa7XV/72tcsHw4AAHxxQ0ZekjIyMvTUU0+p\nr69PBw8ejG7v6+tTamqqpcMBAIAv7pSRl6TKykrV19fL4/EoEolIkmw2m1577TVLhwMAAF/csCK/\nY8cOvfHGG3K5XFbPAwAAYmRYf7v+ggsuIPAAAIwyw1rJn3POObrppps0bdo0ORyO6Pa77rrrlPuu\nW7dObW1tOn78uJYtW6apU6dq5cqVikQiSk9P17p165SQkKCGhgbV1dXJ4XBo8eLFysvLUzgcVmlp\nqTo7O+VwOLRmzRpNmDDhiz9bAADOIMOKfGpqqmbOnPm5D75jxw51dHRoy5YtOnjwoBYtWqQZM2bo\n5ptv1ne+8x3V1NSovr5e1157rWpra1VfXy+n06m8vDzl5OSoublZKSkpqq6uVktLi9avX6+amprP\nPQcAAGeiYUV++fLlX+jgl112mS655BJJ0tixY9Xf369du3bpf//3fyVJ8+bN08aNG3XhhRfK5/Mp\nOTlZkpSdna3du3ertbVV1113nSRp1qxZKisr+0JzAABwJhpW5C+++GLZbLbo7zabTWPGjNGOHTuG\n3M9ms+mss86SJG3dulVXXHGF/vznPyshIUGSNG7cOHV1dam7u1sejye6n8fjUSAQUDAYjG632Wyy\n2+0Kh8NyOoc1NgAAZ7Rh1fLtt9+O/nz06FG1trbq73//+7Af5A9/+IPq6+v1xBNPKCcnJ7r943+O\n92kn2z44OHjKx0pLS5LT6Tjl/T6P3l53TI8HxJPH41Z6+ph4jwFgBHzuJXFiYqLmzp2rjRs3atmy\nZae8/5/+9Cc9+uijeuKJJ+R2u5WcnKyjR48qMTFRfr9fGRkZ8nq9CgQC0X38fr+ysrLk9XoVDAY1\nZcoUhcPhjwY+xSq+t7f/8z6lU+rpCcX8mEC89PSEFAj0xXsMADEy1Jv2YUV+69atJ/z+wQcfyO/3\nn3K/UCikBx98UE8++aTGjPloiJkzZ6qpqUlXX321mpqaNGfOHPl8PlVUVCgUCslms6m9vV3l5eXq\n6+tTY2OjZs+erebmZk2fPn044wIAAA0z8rt37z7hd7fbrYceeuiU+73yyis6ePCgfvzjHysSichm\ns2nt2rUqLy/Xc889p/Hjx2vRokVyOBwqLi5WYWGh7Ha7ioqK5Ha7lZubq5aWFuXn58vlcqmqquqL\nPUsAAM5AtsjJLoB/hoMHD8pmsyklJcXKmb4UK05DdnS8q6JHS5Tk4do8Rrf+npAeWVatSZMuivco\nAGLkS5+ub2tr0913363Dhw8rEokoNTVVDz74oKZOnRqzIQEAQGwNK/Lr169XbW2tJk+eLEn629/+\npvvvv1+bN2+2dDgAAPDFDeuz6+12ezTw0kf/bv6TH28LAABOP8OOfFNTk0KhkEKhkF555RUiDwDA\naW5Yp+vvu+8+/fSnP1VFRYXsdrsyMzNVWVlp9WwAAOBLGNZKvqWlRYmJidq1a5d27NihwcFBbdu2\nzerZAADAlzCsyDc0NGjDhg3R3zdu3KiXXnrJsqEAAMCXN6zIHz9+/IRr8Hb7sHYDAABxNKxr8vPn\nz9eSJUs0bdo0DQ4O6o033jjhi2YAAMDpZ9jfJ3/ZZZfpr3/9q2w2m+69915961vfsno2AADwJQz7\nW+guvfRSXXrppVbOAgAAYoiL6wAAGIrIAwBgKCIPAIChiDwAAIYi8gAAGIrIAwBgKCIPAIChiDwA\nAIYi8gAAGIrIAwBgKCIPAIChiDwAAIYi8gAAGIrIAwBgKCIPAIChiDwAAIYi8gAAGIrIAwBgKMsj\n/8477+jKK6/U5s2bJUmrVq3S1VdfrVtuuUW33HKLtm3bJklqaGhQXl6ebrjhBm3dulWSFA6HVVJS\novz8fBUUFOjAgQNWjwsAgDGcVh58YGBAlZWVmjlz5gnbS0pKNHfu3BPuV1tbq/r6ejmdTuXl5Skn\nJ0fNzc1KSUlRdXW1WlpatH79etXU1Fg5MgAAxrB0Je9yufT444/L6/UOeb89e/bI5/MpOTlZLpdL\n2dnZ2r17t1pbW7VgwQJJ0qxZs9TW1mbluAAAGMXSyNvtdiUmJv7X9qefflq33nqriouL1dvbq2Aw\nKI/HE73d4/EoEAicsN1ms8lutyscDls5MgAAxrD0dP1nufbaa5WamqrMzEw99thj2rBhg7Kysk64\nTyQS+cx9BwcHR2JEAACMMOKRnzFjRvTn+fPna/Xq1Vq4cKFef/316Ha/36+srCx5vV4Fg0FNmTIl\nuoJ3OoceOS0tSU6nI6Yz9/a6Y3o8IJ48HrfS08fEewwAI2DEI3/nnXdq5cqVmjhxonbs2KHJkyfL\n5/OpoqJCoVBINptN7e3tKi8vV19fnxobGzV79mw1Nzdr+vTppzx+b29/zGfu6QnF/JhAvPT0hBQI\n9MV7DAAxMtSbdksjv2/fPlVVVamzs1NOp1NNTU0qKCjQihUrdPbZZys5OVkPPPCAXC6XiouLVVhY\nKLvdrqKiIrndbuXm5qqlpUX5+flyuVyqqqqyclwAAIxii5zsAvgoZcUKpaPjXRU9WqIkD6ftMbr1\n94T0yLJqTZp0UbxHARAjQ63k+cQ7AAAMReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBE\nHgAAQxF5AAAMReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAU\nkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBEHgAAQxF5AAAMReQBADAUkQcAwFBEHgAAQxF5AAAM\nZXnk33nnHV155ZXavHmzJOmDDz5QQUGBbr75Zq1YsULHjh2TJDU0NCgvL0833HCDtm7dKkkKh8Mq\nKSlRfn6+CgoKdODAAavHBQDAGJZGfmBgQJWVlZo5c2Z028MPP6yCggI9/fTTOv/881VfX6+BgQHV\n1tZq06ZNqqur06ZNm3To0CG9/PLLSklJ0TPPPKPbb79d69evt3JcAACMYmnkXS6XHn/8cXm93ui2\nnTt3at68eZKkefPmafv27dqzZ498Pp+Sk5PlcrmUnZ2t3bt3q7W1VQsWLJAkzZo1S21tbVaOCwCA\nUSyNvN1uV2Ji4gnbBgYGlJCQIEkaN26curq61N3dLY/HE72Px+NRIBBQMBiMbrfZbLLb7QqHw1aO\nDACAMeL6F+8ikcjn2j44OGjlOAAAGMU50g+YnJyso0ePKjExUX6/XxkZGfJ6vQoEAtH7+P1+ZWVl\nyev1KhgMasqUKdEVvNM59MhpaUlyOh0xnbm31x3T4wHx5PG4lZ4+Jt5jABgBIx75mTNnqqmpSVdf\nfbWampo0Z84c+Xw+VVRUKBQKyWazqb29XeXl5err61NjY6Nmz56t5uZmTZ8+/ZTH7+3tj/nMPT2h\nmB8TiJeenpACgb54jwEgRoZ6025p5Pft26eqqip1dnbK6XSqqalJ1dXVKi0t1XPPPafx48dr0aJF\ncjgcKi4uVmFhoex2u4qKiuR2u5Wbm6uWlhbl5+fL5XKpqqrKynEBADCKLXKyC+CjlBUrlI6Od1X0\naImSPJy2x+jW3xPSI8uqNWnSRfEeBUCMDLWS5xPvAAAwFJEHAMBQRB4AAEMReQAADEXkAQAwFJEH\nAMBQRB4AAEMReQAADEXkAQAwFJEHAMBQRB4AAEMReQAADEXkAQAwFJEHAMBQRB4AAEMReQAADEXk\nAQAwFJEHAMBQRB4AAEMReQAADEXkAQAwFJEHAMBQRB4AAEMReQAADEXkAQAwFJEHAMBQRB4AAEMR\neQAADEXkAQAwFJEHAMBQzpF+wJ07d+quu+7SRRddpEgkoilTpugHP/iBVq5cqUgkovT0dK1bt04J\nCQlqaGhQXV2dHA6HFi9erLy8vJEeFwCAUWvEIy9Jl112mR5++OHo76tWrVJBQYFycnJUU1Oj+vp6\nXXvttaqtrVV9fb2cTqfy8vKUk5OjsWPHxmNkAABGnbicro9EIif8vnPnTs2bN0+SNG/ePG3fvl17\n9uyRz+dTcnKyXC6XsrOz1dbWFo9xAQAYleKyku/o6NDy5cv1n//8Rz/84Q915MgRJSQkSJLGjRun\nrq4udXd3y+PxRPfxeDwKBALxGBcAgFFpxCN/wQUX6Ec/+pGuuuoqvf/++7rlllsUDoejt396lX+q\n7Z+WlpYkp9MRk1k/1tvrjunxgHjyeNxKTx8T7zEAjIARj3xGRoauuuoqSdLEiRP1la98RXv37tXR\no0eVmJgov9+vjIwMeb3eE1bufr9fWVlZpzx+b29/zGfu6QnF/JhAvPT0hBQI9MV7DAAxMtSb9hG/\nJv/SSy9p48aNkqRAIKDu7m5df/31amxslCQ1NTVpzpw58vl82rt3r0KhkA4fPqz29nZNmzZtpMcF\nAGDUGvGV/Pz581VcXKzXXntN4XBY9913nzIzM/WTn/xEzz//vMaPH69FixbJ4XCouLhYhYWFstvt\nKioqktvNaXMAAIZrxCOfnJysX/3qV/+1/ePV/Sfl5OQoJydnJMYCAMA4fOIdAACGIvIAABiKyAMA\nYCgiDwCAoYg8AACGIvIAABiKyAMAYCgiDwCAoYg8AACGIvIAABiKyAMAYCgiDwCAoYg8AACGIvIA\nABiKyAMAYCgiDwCAoYg8AACGIvIAABiKyAMAYCgiDwCAoYg8AACGIvIAABiKyAMAYCgiDwCAoYg8\nAACGIvIAABiKyAMAYCgiDwCAoZzxHgAATub48eN677398R4DiIkLL/wfORyOEX3M0z7ya9as0Z49\ne2Sz2VRWVqapU6fGeyQAI+S99/ar8qUqjfWmxnsU4Es51HVQFVeXatKki0b0cU/ryO/atUv//Oc/\ntWXLFnV0dKi8vFxbtmyJ91gARtBYb6pSx3viPQYwKp3W1+RbW1u1YMECSdKkSZN06NAhHT58OM5T\nAQAwOpzWkQ8Gg/J4/v8dfFpamoLBYBwnAgBg9DitT9d/WiQSidtjHznUH7fHBmJlNP53fKjrYLxH\nAL60eP13fFpH3uv1nrBy7+rqUnp6+pD7pKePifkc6enZemvrGzE/LoChpadn6+UZz8d7DGDUOq1P\n18+ePVtNTU2SpH379ikjI0NJSUlxngoAgNHhtF7JZ2Vl6Rvf+IaWLFkih8Ohe+65J94jAQAwatgi\n8bzQDQAALHNan64HAABfHJEHAMBQRB4AAEMRecTFmjVrtGTJEt1444166623Trht+/btWrx4sZYs\nWaLa2to4TQiY6Z133tGVV16pzZs3/9dtvPbMQ+Qx4j75nQSVlZW6//77T7j9/vvv14YNG/Tss8+q\npaVFHR0dcZoUMMvAwIAqKys1c+bMz7yd1555iDxG3FDfSfD+++8rNTVVGRkZstlsmjt3rt54gw8i\nAmLB5XLp8ccfl9fr/a/beO2ZichjxA31nQSfvs3j8airq2vEZwRMZLfblZiY+Jm38dozE5FH3A31\nUQ18jAMQH7z2zEDkMeKG+k4Cr9erQCAQvc3v93/mqUUAscVrz0xEHiNuqO8kOO+883T48GF1dnYq\nHA7rj3/8oy6//PJ4jgucEXjtmYmPtUVc/OxnP9POnTuj30nwt7/9TWPGjNGCBQv05ptvqrq6WpK0\ncOFCfe9734vvsIAh9u3bp6qqKnV2dsrpdCojI0Pz58/XhAkTeO0ZisgDAGAoTtcDAGAoIg8AgKGI\nPAAAhiLyAAAYisgDAGAoIg8AgKGIPHCGueuuu3T99dfL7/d/rv3a29t14MABi6b6yN///ncVFBTo\nlltu0Q033KDf/e53lj4eYDpnvAcAMLJ+//vf6y9/+ctJv6jkZF588UXl5uZqwoQJFk0mbdy4UXfd\ndZcuvfRSvf/++7r++uuVk5Nj2eMBpiPywBmkoqJCg4ODWrp0qb773e/qhRdekPTRN45VVlYqJSVF\nzz77rH77298qISFBLpdLDz30kN544w01NjbqrbfeUmlpqX7xi19o+fLlmjlzpv71r38pPz9f27Zt\n06pVq5SQkKD33ntP1dXV6u3t1dq1axUOhxUOh3XPPfcoMzPzpPOtXbs2+vO///1vjR8/3vI/E8Bk\nnK4HziCVlZWy2Wyqrq7Wr3/9az355JPavHmzvv3tb+tXv/qVJOnDDz/Uxo0b9dRTT2n8+PH67W9/\nqwULFigzM1OlpaWaMWPGfx3XZrNFfz5y5Ijq6urk9XpVUlKi++67T3V1dbrnnntUVlZ2yhn/8Y9/\naPHixSovL9e6deti9+SBMxAreeAME4lE1NbWpkAgoKVLlyoSiejYsWOaOHGiJCk1NVW33Xab7Ha7\n/vWvf53/gzUyAAAB9klEQVTwTWTD+RTsrKwsSVJPT4/+8Y9/qLy8PLpff3//Kff/6le/qhdeeEF7\n9+7V8uXL9fLLL+vss8/+Ik8VOOMReeAM5HK55PP5oqv3j/n9fq1du1avvPKK0tLSTjh9/kmfXLkf\nO3bshNsSEhIkSYmJiXK5XKqrqxv2XK+++qoWLlwom82mb37zmxo7dqw6Ojr0zW9+c9jHAPD/OF0P\nnIEuvvhivfXWWwoGg5KkxsZGNTc3q7u7Wx6PR2lpaTp48KBaWlqiEbfb7QqHw5Ikt9utDz74QJLU\n2tr6mY/hdrt13nnnadu2bZI+Og3/i1/8Ysi5fv3rX+u1116TJAWDQXV1den888//8k8YOEPxLXTA\nGebrX/+69u3bp1dffVUbN25UUlKSzjrrLK1du1ZpaWn64Q9/qGAwqIkTJ+qqq67S6tWr9fOf/1xv\nvvmmtmzZorKyMiUlJenee+/V+eefr8svv1ybNm3SH//4R61atUrTpk1TXl6eJOntt9/WT3/6U9ls\nNoXDYa1atUqXXHLJSWfbv3+/7rvvPg0ODqq/v1+33XabFi5cOFJ/NIBxiDwAAIbimjyAEfXII49o\n586dJ1zXlz46w7Bq1ao4TQWYiZU8AACG4i/eAQBgKCIPAIChiDwAAIYi8gAAGIrIAwBgKCIPAICh\n/g/7lrwz75S5nAAAAABJRU5ErkJggg==\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0xaad5240c>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "import data_io\n",
    "reload(data_io)\n",
    "data = data_io.read_as_df(basename)                          # The data are loaded as a Pandas Data Frame\n",
    "sns.countplot(x=\"feature_3\", data=data, palette=\"Greens_d\");\n",
    "#sns.jointplot(\"feature_3\", \"tip\", data=data,size=5, ratio=3, color=\"g\")\n",
    "\n",
    "\n",
    "\n",
    "#data.to_csv(basename + '_train.csv', index=False)           # This allows saving the data in csv format"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 34,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>feature_1</th>\n",
       "      <th>feature_2</th>\n",
       "      <th>feature_3</th>\n",
       "      <th>feature_4</th>\n",
       "      <th>feature_5</th>\n",
       "      <th>feature_6</th>\n",
       "      <th>feature_7</th>\n",
       "      <th>feature_8</th>\n",
       "      <th>feature_9</th>\n",
       "      <th>feature_10</th>\n",
       "      <th>...</th>\n",
       "      <th>feature_1609</th>\n",
       "      <th>feature_1610</th>\n",
       "      <th>feature_1611</th>\n",
       "      <th>feature_1612</th>\n",
       "      <th>feature_1613</th>\n",
       "      <th>feature_1614</th>\n",
       "      <th>feature_1615</th>\n",
       "      <th>feature_1616</th>\n",
       "      <th>feature_1617</th>\n",
       "      <th>target</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>...</td>\n",
       "      <td>1.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>negative</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>negative</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>1.0</td>\n",
       "      <td>negative</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>1.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>negative</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>1.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>negative</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>5 rows × 1618 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "   feature_1  feature_2  feature_3  feature_4  feature_5  feature_6  \\\n",
       "0        0.0        0.0        0.0        0.0        0.0        0.0   \n",
       "1        0.0        0.0        0.0        0.0        0.0        0.0   \n",
       "2        0.0        0.0        0.0        0.0        0.0        0.0   \n",
       "3        0.0        0.0        0.0        0.0        0.0        0.0   \n",
       "4        0.0        0.0        0.0        0.0        0.0        0.0   \n",
       "\n",
       "   feature_7  feature_8  feature_9  feature_10    ...     feature_1609  \\\n",
       "0        0.0        0.0        0.0         0.0    ...              1.0   \n",
       "1        0.0        0.0        0.0         0.0    ...              0.0   \n",
       "2        0.0        0.0        0.0         0.0    ...              0.0   \n",
       "3        0.0        1.0        0.0         0.0    ...              0.0   \n",
       "4        0.0        0.0        0.0         0.0    ...              0.0   \n",
       "\n",
       "   feature_1610  feature_1611  feature_1612  feature_1613  feature_1614  \\\n",
       "0           0.0           0.0           0.0           0.0           0.0   \n",
       "1           0.0           0.0           0.0           0.0           0.0   \n",
       "2           0.0           0.0           0.0           0.0           0.0   \n",
       "3           0.0           0.0           0.0           0.0           0.0   \n",
       "4           0.0           0.0           0.0           0.0           0.0   \n",
       "\n",
       "   feature_1615  feature_1616  feature_1617    target  \n",
       "0           0.0           0.0           0.0  negative  \n",
       "1           0.0           0.0           0.0  negative  \n",
       "2           0.0           0.0           1.0  negative  \n",
       "3           0.0           0.0           0.0  negative  \n",
       "4           0.0           1.0           0.0  negative  \n",
       "\n",
       "[5 rows x 1618 columns]"
      ]
     },
     "execution_count": 34,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "data.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 35,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>feature_1</th>\n",
       "      <th>feature_2</th>\n",
       "      <th>feature_3</th>\n",
       "      <th>feature_4</th>\n",
       "      <th>feature_5</th>\n",
       "      <th>feature_6</th>\n",
       "      <th>feature_7</th>\n",
       "      <th>feature_8</th>\n",
       "      <th>feature_9</th>\n",
       "      <th>feature_10</th>\n",
       "      <th>...</th>\n",
       "      <th>feature_1608</th>\n",
       "      <th>feature_1609</th>\n",
       "      <th>feature_1610</th>\n",
       "      <th>feature_1611</th>\n",
       "      <th>feature_1612</th>\n",
       "      <th>feature_1613</th>\n",
       "      <th>feature_1614</th>\n",
       "      <th>feature_1615</th>\n",
       "      <th>feature_1616</th>\n",
       "      <th>feature_1617</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>count</th>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>...</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "      <td>3845.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>mean</th>\n",
       "      <td>0.009623</td>\n",
       "      <td>0.022627</td>\n",
       "      <td>0.016905</td>\n",
       "      <td>0.035891</td>\n",
       "      <td>0.061638</td>\n",
       "      <td>0.118336</td>\n",
       "      <td>0.033030</td>\n",
       "      <td>0.008062</td>\n",
       "      <td>0.037191</td>\n",
       "      <td>0.017425</td>\n",
       "      <td>...</td>\n",
       "      <td>0.049935</td>\n",
       "      <td>0.132380</td>\n",
       "      <td>0.018986</td>\n",
       "      <td>0.080104</td>\n",
       "      <td>0.057997</td>\n",
       "      <td>0.072042</td>\n",
       "      <td>0.005982</td>\n",
       "      <td>0.056177</td>\n",
       "      <td>0.065280</td>\n",
       "      <td>0.067620</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>std</th>\n",
       "      <td>0.097636</td>\n",
       "      <td>0.148730</td>\n",
       "      <td>0.128933</td>\n",
       "      <td>0.186042</td>\n",
       "      <td>0.240529</td>\n",
       "      <td>0.323047</td>\n",
       "      <td>0.178738</td>\n",
       "      <td>0.089440</td>\n",
       "      <td>0.189255</td>\n",
       "      <td>0.130867</td>\n",
       "      <td>...</td>\n",
       "      <td>0.217839</td>\n",
       "      <td>0.338947</td>\n",
       "      <td>0.136492</td>\n",
       "      <td>0.271489</td>\n",
       "      <td>0.233769</td>\n",
       "      <td>0.258590</td>\n",
       "      <td>0.077120</td>\n",
       "      <td>0.230293</td>\n",
       "      <td>0.247051</td>\n",
       "      <td>0.251126</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>min</th>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>...</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25%</th>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>...</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>50%</th>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>...</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>75%</th>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>...</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>max</th>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>...</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>1.000000</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>8 rows × 1617 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "         feature_1    feature_2    feature_3    feature_4    feature_5  \\\n",
       "count  3845.000000  3845.000000  3845.000000  3845.000000  3845.000000   \n",
       "mean      0.009623     0.022627     0.016905     0.035891     0.061638   \n",
       "std       0.097636     0.148730     0.128933     0.186042     0.240529   \n",
       "min       0.000000     0.000000     0.000000     0.000000     0.000000   \n",
       "25%       0.000000     0.000000     0.000000     0.000000     0.000000   \n",
       "50%       0.000000     0.000000     0.000000     0.000000     0.000000   \n",
       "75%       0.000000     0.000000     0.000000     0.000000     0.000000   \n",
       "max       1.000000     1.000000     1.000000     1.000000     1.000000   \n",
       "\n",
       "         feature_6    feature_7    feature_8    feature_9   feature_10  \\\n",
       "count  3845.000000  3845.000000  3845.000000  3845.000000  3845.000000   \n",
       "mean      0.118336     0.033030     0.008062     0.037191     0.017425   \n",
       "std       0.323047     0.178738     0.089440     0.189255     0.130867   \n",
       "min       0.000000     0.000000     0.000000     0.000000     0.000000   \n",
       "25%       0.000000     0.000000     0.000000     0.000000     0.000000   \n",
       "50%       0.000000     0.000000     0.000000     0.000000     0.000000   \n",
       "75%       0.000000     0.000000     0.000000     0.000000     0.000000   \n",
       "max       1.000000     1.000000     1.000000     1.000000     1.000000   \n",
       "\n",
       "           ...       feature_1608  feature_1609  feature_1610  feature_1611  \\\n",
       "count      ...        3845.000000   3845.000000   3845.000000   3845.000000   \n",
       "mean       ...           0.049935      0.132380      0.018986      0.080104   \n",
       "std        ...           0.217839      0.338947      0.136492      0.271489   \n",
       "min        ...           0.000000      0.000000      0.000000      0.000000   \n",
       "25%        ...           0.000000      0.000000      0.000000      0.000000   \n",
       "50%        ...           0.000000      0.000000      0.000000      0.000000   \n",
       "75%        ...           0.000000      0.000000      0.000000      0.000000   \n",
       "max        ...           1.000000      1.000000      1.000000      1.000000   \n",
       "\n",
       "       feature_1612  feature_1613  feature_1614  feature_1615  feature_1616  \\\n",
       "count   3845.000000   3845.000000   3845.000000   3845.000000   3845.000000   \n",
       "mean       0.057997      0.072042      0.005982      0.056177      0.065280   \n",
       "std        0.233769      0.258590      0.077120      0.230293      0.247051   \n",
       "min        0.000000      0.000000      0.000000      0.000000      0.000000   \n",
       "25%        0.000000      0.000000      0.000000      0.000000      0.000000   \n",
       "50%        0.000000      0.000000      0.000000      0.000000      0.000000   \n",
       "75%        0.000000      0.000000      0.000000      0.000000      0.000000   \n",
       "max        1.000000      1.000000      1.000000      1.000000      1.000000   \n",
       "\n",
       "       feature_1617  \n",
       "count   3845.000000  \n",
       "mean       0.067620  \n",
       "std        0.251126  \n",
       "min        0.000000  \n",
       "25%        0.000000  \n",
       "50%        0.000000  \n",
       "75%        0.000000  \n",
       "max        1.000000  \n",
       "\n",
       "[8 rows x 1617 columns]"
      ]
     },
     "execution_count": 35,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "data.describe() "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Features Variances\n",
    "\n",
    "We're gonna plot the features variances to see if some feature are irreleavant(very low variance). You can use that to reduce the dimensionality of each line."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 42,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Dimensions X_train= (3845, 1617) y_train= (3845,)\n",
      "[ 0.00953029  0.02211482  0.01661929 ...,  0.05302101  0.06101816\n",
      "  0.06304778]\n"
     ]
    }
   ],
   "source": [
    "import numpy as np\n",
    "X_train = data.drop('target', axis=1).values \n",
    "y_train = data['target'].values                         # These are the target values encoded as categorical variables\n",
    "print 'Dimensions X_train=', X_train.shape, 'y_train=', y_train.shape\n",
    "var_features = np.var(X_train, axis=0)\n",
    "print var_features"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "RESTRICTION DE MATRICE"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 61,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "['negative' 'negative' 'negative' ..., 'negative' 'negative' 'negative']\n",
      "['negative' 'positive']\n",
      "[False False False ..., False False False]\n",
      "Number of positive elements: 135\n",
      "Number of negative elements: 3710\n",
      "total: 3845\n",
      "135\n",
      "3710\n"
     ]
    }
   ],
   "source": [
    "labels=np.unique(y_train)\n",
    "print y_train\n",
    "print labels\n",
    "print (y_train == 'positive')\n",
    "n_pos=sum(y_train == 'positive')\n",
    "n_neg=sum(y_train == 'negative')\n",
    "print (\"Number of positive elements: %s\" % n_pos)\n",
    "print (\"Number of negative elements: %s\" % n_neg)\n",
    "print (\"total: %s\" % len(y_train ))\n",
    "#elems = x[(y == 'positive')]\n",
    "        #class_xs.append((yi, elems))\n",
    "y_pos=y_train [(y_train == 'positive')]\n",
    "y_neg= y_train [(y_train == 'negative')]\n",
    "print len (y_pos)\n",
    "print len (y_neg) \n",
    "idx= np.random.shuffle( range y_pos)\n",
    "\n",
    "    "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": 37,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "import matplotlib.pylab as plt\n",
    "#sns.jointplot(\"feature_33\", \"feature_3\", data=data,stat_func=spearmanr, color=\"m\")\n",
    "#sns.jointplot(\"feature_333\", \"feature_123\",data=data, color=\"k\").plot_joint(sns.kdeplot, zorder=0, n_levels=6)\n",
    "\n",
    "#sns.countplot(x=\"feature_3\", data=data, palette=\"Greens_d\");\n",
    "#sns.jointplot(\"feature_3\", \"tip\", data=data, kind=\"hex\")\n",
    "#plt.plot(var_features, 'b*')\n",
    "#plt.ylabel(\"Variance\")\n",
    "#plt.xlabel(\"feature\")\n",
    "#plt.title(\"Variance des features\")\n",
    "#plt.show()\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 41,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Info file NOT found : /home/sara.hadj-ali/mini projet/starting_kit_hivaV2/public_data/hiva_public.info\n"
     ]
    },
    {
     "ename": "MemoryError",
     "evalue": "",
     "output_type": "error",
     "traceback": [
      "\u001b[1;31m---------------------------------------------------------------------------\u001b[0m",
      "\u001b[1;31mMemoryError\u001b[0m                               Traceback (most recent call last)",
      "\u001b[1;32m<ipython-input-41-f0d5c4e0e471>\u001b[0m in \u001b[0;36m<module>\u001b[1;34m()\u001b[0m\n\u001b[0;32m     52\u001b[0m             \u001b[1;32mprint\u001b[0m \u001b[0mDF\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mdescribe\u001b[0m\u001b[1;33m(\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0;32m     53\u001b[0m \u001b[1;33m\u001b[0m\u001b[0m\n\u001b[1;32m---> 54\u001b[1;33m \u001b[0mD\u001b[0m\u001b[1;33m=\u001b[0m \u001b[0mDataManager\u001b[0m\u001b[1;33m(\u001b[0m\u001b[0mbasename\u001b[0m\u001b[1;33m,\u001b[0m \u001b[1;34m\"./\"\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0m\u001b[0;32m     55\u001b[0m \u001b[0mD\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mDataStats\u001b[0m\u001b[1;33m(\u001b[0m\u001b[1;34m'train'\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0;32m     56\u001b[0m \u001b[0mD\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mShowScatter\u001b[0m\u001b[1;33m(\u001b[0m\u001b[1;36m1\u001b[0m\u001b[1;33m,\u001b[0m \u001b[1;36m2\u001b[0m\u001b[1;33m,\u001b[0m \u001b[1;34m'train'\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n",
      "\u001b[1;32m/home/sara.hadj-ali/mini projet/starting_kit_hivaV2/sample_code/data_manager.pyc\u001b[0m in \u001b[0;36m__init__\u001b[1;34m(self, basename, input_dir, verbose, replace_missing, filter_features, max_samples)\u001b[0m\n\u001b[0;32m     82\u001b[0m                 \u001b[0mYtr\u001b[0m \u001b[1;33m=\u001b[0m \u001b[0mYtr\u001b[0m\u001b[1;33m[\u001b[0m\u001b[1;36m0\u001b[0m\u001b[1;33m:\u001b[0m\u001b[0mmax_samples\u001b[0m\u001b[1;33m]\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0;32m     83\u001b[0m                 \u001b[0mXva\u001b[0m \u001b[1;33m=\u001b[0m \u001b[0mself\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mloadData\u001b[0m \u001b[1;33m(\u001b[0m\u001b[0mos\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mpath\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mjoin\u001b[0m\u001b[1;33m(\u001b[0m\u001b[0mself\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0minput_dir\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mbasename\u001b[0m \u001b[1;33m+\u001b[0m \u001b[1;34m'_valid.data'\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mverbose\u001b[0m\u001b[1;33m=\u001b[0m\u001b[0mverbose\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mreplace_missing\u001b[0m\u001b[1;33m=\u001b[0m\u001b[0mreplace_missing\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[1;32m---> 84\u001b[1;33m                 \u001b[0mXte\u001b[0m \u001b[1;33m=\u001b[0m \u001b[0mself\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mloadData\u001b[0m \u001b[1;33m(\u001b[0m\u001b[0mos\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mpath\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mjoin\u001b[0m\u001b[1;33m(\u001b[0m\u001b[0mself\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0minput_dir\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mbasename\u001b[0m \u001b[1;33m+\u001b[0m \u001b[1;34m'_test.data'\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mverbose\u001b[0m\u001b[1;33m=\u001b[0m\u001b[0mverbose\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mreplace_missing\u001b[0m\u001b[1;33m=\u001b[0m\u001b[0mreplace_missing\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0m\u001b[0;32m     85\u001b[0m            \u001b[1;31m# Normally, feature selection should be done as part of a pipeline.\u001b[0m\u001b[1;33m\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0;32m     86\u001b[0m            \u001b[1;31m# However, here we do it as a preprocessing for efficiency reason\u001b[0m\u001b[1;33m\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n",
      "\u001b[1;32m/home/sara.hadj-ali/mini projet/starting_kit_hivaV2/sample_code/data_manager.pyc\u001b[0m in \u001b[0;36mloadData\u001b[1;34m(self, filename, verbose, replace_missing)\u001b[0m\n\u001b[0;32m    129\u001b[0m                 \u001b[0mdata_func\u001b[0m \u001b[1;33m=\u001b[0m \u001b[1;33m{\u001b[0m\u001b[1;34m'dense'\u001b[0m\u001b[1;33m:\u001b[0m\u001b[0mdata_io\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mdata\u001b[0m\u001b[1;33m,\u001b[0m \u001b[1;34m'sparse'\u001b[0m\u001b[1;33m:\u001b[0m\u001b[0mdata_io\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mdata_sparse\u001b[0m\u001b[1;33m,\u001b[0m \u001b[1;34m'sparse_binary'\u001b[0m\u001b[1;33m:\u001b[0m\u001b[0mdata_io\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mdata_binary_sparse\u001b[0m\u001b[1;33m}\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0;32m    130\u001b[0m \u001b[1;33m\u001b[0m\u001b[0m\n\u001b[1;32m--> 131\u001b[1;33m                 \u001b[0mdata\u001b[0m \u001b[1;33m=\u001b[0m \u001b[0mdata_func\u001b[0m\u001b[1;33m[\u001b[0m\u001b[0mself\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0minfo\u001b[0m\u001b[1;33m[\u001b[0m\u001b[1;34m'format'\u001b[0m\u001b[1;33m]\u001b[0m\u001b[1;33m]\u001b[0m\u001b[1;33m(\u001b[0m\u001b[0mfilename\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mself\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0minfo\u001b[0m\u001b[1;33m[\u001b[0m\u001b[1;34m'feat_num'\u001b[0m\u001b[1;33m]\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0m\u001b[0;32m    132\u001b[0m \u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0;32m    133\u001b[0m                 \u001b[1;31m# INPORTANT: when we replace missing values we double the number of variables\u001b[0m\u001b[1;33m\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n",
      "\u001b[1;32m/home/sara.hadj-ali/mini projet/starting_kit_hivaV2/sample_code/data_io.pyc\u001b[0m in \u001b[0;36mdata\u001b[1;34m(filename, nbr_features, verbose)\u001b[0m\n\u001b[0;32m    201\u001b[0m         \u001b[1;34m''' The 2nd parameter makes possible a using of the 3 functions of data reading (data, data_sparse, data_binary_sparse) without changing parameters'''\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0;32m    202\u001b[0m         \u001b[1;32mif\u001b[0m \u001b[0mverbose\u001b[0m\u001b[1;33m:\u001b[0m \u001b[1;32mprint\u001b[0m \u001b[1;33m(\u001b[0m\u001b[0mnp\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0marray\u001b[0m\u001b[1;33m(\u001b[0m\u001b[0mdata_converter\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mfile_to_array\u001b[0m\u001b[1;33m(\u001b[0m\u001b[0mfilename\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[1;32m--> 203\u001b[1;33m         \u001b[1;32mreturn\u001b[0m \u001b[0mnp\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0marray\u001b[0m\u001b[1;33m(\u001b[0m\u001b[0mdata_converter\u001b[0m\u001b[1;33m.\u001b[0m\u001b[0mfile_to_array\u001b[0m\u001b[1;33m(\u001b[0m\u001b[0mfilename\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mdtype\u001b[0m\u001b[1;33m=\u001b[0m\u001b[0mfloat\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0m\u001b[0;32m    204\u001b[0m \u001b[1;33m\u001b[0m\u001b[0m\n\u001b[0;32m    205\u001b[0m \u001b[1;32mdef\u001b[0m \u001b[0mdata_sparse\u001b[0m \u001b[1;33m(\u001b[0m\u001b[0mfilename\u001b[0m\u001b[1;33m,\u001b[0m \u001b[0mnbr_features\u001b[0m\u001b[1;33m)\u001b[0m\u001b[1;33m:\u001b[0m\u001b[1;33m\u001b[0m\u001b[0m\n",
      "\u001b[1;31mMemoryError\u001b[0m: "
     ]
    }
   ],
   "source": [
    "# Add the sample code in the path\n",
    "mypath = \"../sample_code\"\n",
    "from sys import argv, path\n",
    "from os.path import abspath\n",
    "path.append(abspath(mypath))\n",
    "\n",
    "# Graphic routines\n",
    "import seaborn as sns; sns.set()\n",
    "\n",
    "# Data types\n",
    "import pandas as pd\n",
    "\n",
    "# Mother class\n",
    "import data_manager\n",
    "\n",
    "class DataManager(data_manager.DataManager):\n",
    "    '''This class reads and displays data. \n",
    "       With class inheritance, we do not need to redefine the constructor,\n",
    "       unless we want to add or change some data members.\n",
    "       '''\n",
    "       \n",
    "#    def __init__(self, basename=\"\", input_dir=\"\"):\n",
    "#        ''' New contructor.'''\n",
    "#        DataManager.__init__(self, basename, input_dir)\n",
    "        # So something here\n",
    "    \n",
    "    def toDF(self, set_name):\n",
    "        ''' Change a given data subset to a data Panda's frame.\n",
    "            set_name is 'train', 'valid' or 'test'.'''\n",
    "        DF = pd.DataFrame(self.data['X_'+set_name])\n",
    "        # For training examples, we can add the target values as\n",
    "        # a last column: this is convenient to use seaborn\n",
    "        # Look at http://seaborn.pydata.org/tutorial/axis_grids.html for other ideas\n",
    "        if set_name == 'train':\n",
    "            Y = self.data['Y_train']\n",
    "            DF = DF.assign(target=Y)          \n",
    "        return DF\n",
    "\n",
    "   \n",
    "    def DataStats(self, set_name):\n",
    "        ''' Display simple data statistics'''\n",
    "        DF = self.toDF(set_name)\n",
    "        print DF.describe()\n",
    "    \n",
    "    def ShowScatter(self, var1, var2, set_name):\n",
    "        ''' Show scatter plots.'''\n",
    "        DF = self.toDF(set_name)\n",
    "        if set_name == 'train':\n",
    "            sns.pairplot(DF.ix[:, [var1, var2, \"target\"]], hue=\"target\")\n",
    "        else:\n",
    "            sns.pairplot(DF.ix[:, [var1, var2]])\n",
    "            print DF.describe()\n",
    " \n",
    "D= DataManager(basename, \"./\")\n",
    "D.DataStats('train')\n",
    "D.ShowScatter(1, 2, 'train')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "X_train = data.drop('target', axis=1).values            # This is the data matrix you already loaded (training data)\n",
    "y_train = data['target'].values                         # These are the target values encoded as categorical variables\n",
    "print 'Dimensions X_train=', X_train.shape, 'y_train=', y_train.shape\n",
    "X_test = data_io.read_as_df(basename, 'test')\n",
    "X_valid = data_io.read_as_df(basename, 'valid')"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    " RESTRICTION DE MATRICE"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "#labels=np.unique(y_train)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "# You can remove features with low variance by doing this : \n",
    "\n",
    "from sklearn.feature_selection import VarianceThreshold\n",
    "sel = VarianceThreshold(threshold=(0.05))\n",
    "X_train_modify = sel.fit_transform(X_train)\n",
    "\n",
    "print \"Shape before reudction : \", X_train.shape\n",
    "print \"Shape after reduction : \", X_train_modify.shape"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Building a predictive model"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Data matrices for training and making predictions."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The initial classifier in your starting kit (in the sample_code directory)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "import classifier\n",
    "reload(classifier)                               # If you make changes to your code you have to reload it\n",
    "from classifier import Classifier\n",
    "Classifier??\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Train, run, and save your classifier and your predictions. If you saved a trained model and/or prediction results, the evaluation script will look for those and use those in priority [(1) use saved predictions; (2) if no predictions, use saved model, do not retrain, just test; (3) if neither, train and test model from scratch]. Compute the predictions with predict_proba, this is more versatile."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "import pdb\n",
    "import pandas as pd\n",
    "result_dir = 'res/'\n",
    "outname = result_dir + dataname\n",
    "print(type(y_train))\n",
    "\n",
    "clf = Classifier()\n",
    "print(X_train,y_train)\n",
    "clf.fit(X_train, y_newtrain)\n",
    "\n",
    "Y_valid = clf.predict_proba(X_valid)\n",
    "Y_test = clf.predict_proba(X_test)\n",
    "clf.save(outname)\n",
    "#clf.load(outname) # Uncomment to check reloading works\n",
    "data_io.write(outname + '_valid.predict', Y_valid)\n",
    "data_io.write(outname + '_test.predict', Y_test)\n",
    "# !ls $outname*"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "# Features importance plot\n",
    "# To be more readble, we only plot the 10 most important features\n",
    "import matplotlib.pylab as plt \n",
    "importances = clf.feature_importances_\n",
    "std = np.std([tree.feature_importances_ for tree in clf.estimators_],\n",
    "             axis=0)\n",
    "number_features = 10\n",
    "indices = np.argsort(importances)[::-1][:number_features]\n",
    "\n",
    "# Print the feature ranking\n",
    "print(\"Feature ranking:\")\n",
    "\n",
    "for f in range(number_features):\n",
    "    print(\"%d. feature %d (%f)\" % (f + 1, indices[f], importances[indices[f]]))\n",
    "\n",
    "# Plot the feature importances of the forest\n",
    "plt.figure()\n",
    "plt.title(\"Feature importances\")\n",
    "plt.bar(range(number_features), importances[indices],\n",
    "       color=\"r\", yerr=std[indices], align=\"center\")\n",
    "plt.xticks(range(number_features), indices)\n",
    "plt.xlim([-1, number_features])\n",
    "plt.show()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Compute the training accuracy."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "from sklearn.metrics import accuracy_score\n",
    "from sklearn.metrics import confusion_matrix\n",
    "# Directly predicts the (categorical) class labels\n",
    "y_predict = clf.predict(X_train)                   \n",
    "print 'Training accuracy = ', accuracy_score(y_train, y_predict)\n",
    "class_labels = clf.get_classes()     \n",
    "print 'Class labels=', class_labels\n",
    "print 'Confusion matrix [known in lines, predicted in columns]=\\n',confusion_matrix(y_train, y_predict, class_labels)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Compute cross-validation accuracy. This is usually worse than the training accuracy. Notice that we internally split the training data into training and validation set (this is because we do NOT have the labels of X_valid and X_test)."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "ADVANCED: Sklearn does not have multi-class metrics, this shows how libscore metrics work."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "import libscores\n",
    " \n",
    "# To evaluate results with multi-class metrics, the targets must be encoded as one vs. the rest\n",
    "Y_train, C = libscores.onehot(y_train)                                   \n",
    "print 'Dimensions Y_train=', Y_train.shape, 'Class labels=', C\n",
    "assert((class_labels==C).all()) # Just to make sure the labels of the classifier are in the right order\n",
    "# Note: if all went well, you should recover public_data/iris_train.solution\n",
    "# You had it all along, but to show you some nice plots we loaded the data as a data frame so we lost it!\n",
    "\n",
    "from libscores import bac_metric \n",
    "from libscores import pac_metric \n",
    "# Predicts probabilities, a matrix patnum x classnum \n",
    "# As solution, you must use Y_train, not y_train\n",
    "y_predict_proba = clf.predict_proba(X_train)      \n",
    "print 'Training balanced accuracy = ', bac_metric(Y_train, y_predict_proba, task='multiclass.classification')\n",
    "print 'Training probabilistic accuracy = ', pac_metric(Y_train, y_predict_proba, task='multiclass.classification')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "from sklearn.cross_validation import StratifiedShuffleSplit\n",
    "# This is just an example of 2-fold cross-validation\n",
    "skf = StratifiedShuffleSplit(y_train, n_iter=2, test_size=0.5, random_state=61)\n",
    "i=0\n",
    "for idx_t, idx_v in skf:\n",
    "    i=i+1\n",
    "    Xtr = X_train[idx_t]\n",
    "    Ytr = y_train[idx_t]\n",
    "    Xva = X_train[idx_v]\n",
    "    Yva = y_train[idx_v]\n",
    "    clf = Classifier()\n",
    "    clf.fit(Xtr, Ytr)\n",
    "    Y_predict = clf.predict(Xva)\n",
    "    print 'Fold', i, 'validation accuracy = ', accuracy_score(Y_predict, Yva)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Unit testing"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "It is <b><span style=\"color:red\">important that you test your submission files before submitting them</span></b>. All you have to do to make a submission is modify the file <code>classifier.py</code> in the <code>sample_code/</code> directory, then run this test to make sure everything works fine. This is the actual program that will be run on the server to test your submission.  The program looks for saved results and saved models in the subdirectory <code>res/</code>. If it finds them, it will use them: (1) If results are found, then are copied to the output directory; (2) If no results but a trained model is found, it is reloaded and no training occurs; (3) If nothing is found a fresh model is trained and tested."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false,
    "scrolled": true
   },
   "outputs": [],
   "source": [
    "outdir = '../outputs'         # If you use result_dir as output directory, your submission will include your results"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false,
    "scrolled": true
   },
   "outputs": [],
   "source": [
    "!python run.py $datadir $outdir"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "collapsed": true
   },
   "source": [
    "## Making your submission\n",
    "\n",
    "The test program <code>run.py</code> prepares your <code>zip</code> file, ready to go. You find it in the directory above where you ran your program. For large datasets, we recommend that <b><span style=\"color:red\">you do NOT bundle the data with your submission</span></b>. The data directory is passed as an argument to run.py, and it is already there on the test server."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "anaconda-cloud": {},
  "kernelspec": {
   "display_name": "Python 2",
   "language": "python",
   "name": "python2"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 2
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython2",
   "version": "2.7.11"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 0
}
