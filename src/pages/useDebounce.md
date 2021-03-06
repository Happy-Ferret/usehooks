---
templateKey: post
title: useDebounce
date: "2018-11-09"
gist: https://gist.github.com/gragland/e50346f02e7edf4f81cc0bda33d3cae6
sandbox: https://codesandbox.io/s/711r1zmq50
code:
  "import { useState, useEffect, useRef } from 'react';\r\n\r\n// Usage\r\nfunction
  App() {\r\n  // State and setters for ...\r\n  // Search term\r\n  const [searchTerm,
  setSearchTerm] = useState('');\r\n  // API search results\r\n  const [results, setResults]
  = useState([]);\r\n  // Searching status (whether there is pending API request)\r\n
  \ const [isSearching, setIsSearching] = useState(false);\r\n  // Debounce search
  term so that it only gives us latest value ...\r\n  // ... if searchTerm has not
  been updated within last 500ms.\r\n  // The goal is to only have the API call fire
  when user stops typing ...\r\n  // ... so that we aren't hitting our API rapidly.\r\n
  \ const debouncedSearchTerm = useDebounce(searchTerm, 500);\r\n  \r\n  // Effect
  for API call \r\n  useEffect(\r\n    () => {\r\n      if (debouncedSearchTerm) {\r\n
  \       setIsSearching(true);\r\n        searchCharacters(debouncedSearchTerm).then(results
  => {\r\n          setIsSearching(false);\r\n          setResults(results.data.results);\r\n
  \       });\r\n      } else {\r\n        setResults([]);\r\n      }\r\n    },\r\n
  \   [debouncedSearchTerm] // Only call effect if debounced search term changes\r\n
  \ );\r\n\r\n  return (\r\n    <div>\r\n      <input\r\n        placeholder=\"Search
  Marvel Comics\"\r\n        onChange={e => setSearchTerm(e.target.value)}\r\n      />\r\n
  \ \r\n      {isSearching && <div>Searching ...</div>}\r\n\r\n      {results.map(result
  => (\r\n        <div key={result.id}>\r\n          <h4>{result.title}</h4>\r\n          <img\r\n
  \           src={`${result.thumbnail.path}/portrait_incredible.${\r\n              result.thumbnail.extension\r\n
  \           }`}\r\n          />\r\n        </div>\r\n      ))}\r\n    </div>\r\n
  \ );\r\n}\r\n\r\n// API search function\r\nfunction searchCharacters(search) {\r\n
  \ const apiKey = 'f9dfb1e8d466d36c27850bedd2047687';\r\n  return fetch(\r\n    `https://gateway.marvel.com/v1/public/comics?apikey=${apiKey}&titleStartsWith=${search}`,\r\n
  \   {\r\n      method: 'GET'\r\n    }\r\n  ).then(r => r.json());\r\n}\r\n       \r\n//
  Hook\r\nfunction useDebounce(value, delay) {\r\n  // State and setters for debounced
  value\r\n  const [debouncedValue, setDebouncedValue] = useState(value);\r\n\r\n
  \ useEffect(\r\n    () => {\r\n      // Update debounced value after delay\r\n      const
  handler = setTimeout(() => {\r\n        setDebouncedValue(value);\r\n      }, delay);\r\n\r\n
  \     // Cancel the timeout if value changes (also on delay change or unmount)\r\n
  \     // This is how we prevent debounced value from updating if value is changed
  ...\r\n      // .. within the delay period. Timeout gets cleared and restarted.\r\n
  \     return () => {\r\n        clearTimeout(handler);\r\n      };\r\n    },\r\n
  \   [value, delay] // Only re-call effect if value or delay changes\r\n  );\r\n\r\n
  \ return debouncedValue;\r\n}"
---

This hook allows you to debounce any fast changing value. The debounced value will only reflect the latest value when the useDebounce hook has not been called for the specified time period. When used in conjuction with useEffect, as we do in the recipe below, you can easily ensure that expensive operations like API calls are not executed too frequently. The example below allows you to search the Marvel Comic API and uses useDebounce to prevent API calls from being fired on every keystroke. Be sure to theck out the [CodeSandbox demo](https://codesandbox.io/s/711r1zmq50) for this one. Hook code and inspiration from [github.com/xnimorz/use-debounce](https://github.com/xnimorz/use-debounce).
