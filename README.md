1. Data Input Interface :
 Dynamic Scorecard Tool using React, Node.js, and MongoDB:

Frontend (React)

jsx
import React, { useState } from 'react';
import axios from 'axios';

function DataInputForm() {
  const [criteriaCategories, setCriteriaCategories] = useState([]);
  const [inputData, setInputData] = useState({});

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      const response = await axios.post('/api/input-data', inputData);
      console.log(response.data);
    } catch (error) {
      console.error(error);
    }
  };

  const handleCategoryChange = (event) => {
    const selectedCategories = event.target.value;
    setCriteriaCategories(selectedCategories);
  };

  const handleInputChange = (event) => {
    const { name, value } = event.target;
    setInputData({ ...inputData, [name]: value });
  };

  return (
    <form onSubmit={handleSubmit}>
      <select multiple value={criteriaCategories} onChange={handleCategoryChange}>
        <option value="productivity">Productivity</option>
        <option value="quality">Quality</option>
        <option value="timeliness">Timeliness</option>
      </select>
      {criteriaCategories.map((category) => (
        <div key={category}>
          <label>{category}</label>
          <input type="text" name={category} value={inputData[category]} onChange={handleInputChange} />
        </div>
      ))}
      <button type="submit">Submit</button>
    </form>
  );
}

export default DataInputForm;


Backend (Node.js)

const express = require('express');
const app = express();
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost/scorecard', { useNewUrlParser: true, useUnifiedTopology: true });

const inputDataSchema = new mongoose.Schema({
  criteriaCategories: [{ type: String }],
  inputData: [{ type: String }],
});

const InputData = mongoose.model('InputData', inputDataSchema);

app.post('/api/input-data', async (req, res) => {
  try {
    const inputData = new InputData(req.body);
    await inputData.save();
    res.send({ message: 'Input data saved successfully' });
  } catch (error) {
    console.error(error);
    res.status(500).send({ message: 'Error saving input data' });
  }
});

app.listen(3000, () => {
  console.log('Server listening on port 3000');
});

