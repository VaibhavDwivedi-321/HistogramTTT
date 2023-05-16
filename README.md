![image](https://github.com/VaibhavDwivedi-321/HistogramTTT/assets/86218655/25f75ec1-bee0-4559-8475-bbff0217dd55)
DEPLOYED ON RENDER: https://histogram-ttt-vaibhav.onrender.com



### CODE EXPLANATION:

**Several modules and libraries are used in the following code, and we import them all here:**
```
import React, { useState, useRef, useEffect } from "react";
import axios from "axios";
import { saveAs } from "file-saver";
import Chart from "chart.js/auto";

```


**then we created a functional component named App and used react hooks to store the title and histogram data.**

`function App() {`
```
  const [title, setTitle] = useState("CLICK ME PLEASE");
  const [histogramData, setHistogramData] = useState(null);
  const canvasRef = useRef(null);

```

**then we utilise the useEffect hook and axios within the same component to take care of what the user sees initially and to manipulate the data that is obtained by the supplied api.**

```
useEffect(() => {
    if (histogramData && canvasRef.current) {
      const ctx = canvasRef.current.getContext("2d");
      new Chart(ctx, {
        type: "bar",
        data: histogramData,
        options: {
          scales: {
            y: {
              beginAtZero: true,
              precision: 0
            }
          }
        }
      });
    }
  }, [histogramData]);

  const fetchData = async () => {
    try {
      const response = await axios.get(
        "https://www.terriblytinytales.com/test.txt"
      );
```


**After collecting the data from the provided API, we separate the terms and count their frequency before sorting them in decreasing order.**

      const textContent = response.data;
      const words = textContent.split(/\s+/);

      const wordFrequencyMap = {};
      words.forEach((word) => {
        if (wordFrequencyMap[word]) {
          wordFrequencyMap[word] += 1;
        } else {
          wordFrequencyMap[word] = 1;
        }
      });

      const sortedWords = Object.keys(wordFrequencyMap).sort(
        (a, b) => wordFrequencyMap[b] - wordFrequencyMap[a]
      );

      const topWords = sortedWords.slice(0, 20);
      const frequencyData = topWords.map((word) => wordFrequencyMap[word]);

      const chartData = {
        labels: topWords,
        datasets: [
          {
            label: "Word Frequency",
            data: frequencyData,
            backgroundColor: "rgba(75,192,192,0.6)"
          }
        ]
      };
      setTitle("HISTOGRAM ACCORDING TO FREQUENCEY");
      setHistogramData(chartData);
    } catch (error) {
      console.error("Error fetching data:", error);
    }
  };


**When the "export" button is pressed, we take care of how the data is exported here:**

 ```
 const exportData = () => {
    if (!histogramData) {
      console.error("No histogram data available");
      return;
    }
```

    const csvContent = [
      ["Word", "Frequency"],
      ...histogramData.labels.map((word, index) => [
        word,
        histogramData.datasets[0].data[index]
      ])
    ]
      .map((row) => row.join(","))
      .join("\n");

    const blob = new Blob([csvContent], { type: "text/csv;charset=utf-8" });
    saveAs(blob, "word_frequency.csv");
  };


**Then, by re displaying the app component, we ultimately render what the app component is returning, which is a title and a button upon clicking which the histogram appears.**
```
 return (
    <div>
      <h1>{title}</h1>
      <button onClick={fetchData}>Submit</button>
      {histogramData && (
        <div>
          <canvas ref={canvasRef} />
          <button onClick={exportData}>Export</button>
        </div>
      )}
    </div>
  );
}

export default App;
```

