<template>
  <div class="dropdown">
    <!-- Dropdown button with arrow, disabled if there are no queries -->
    <button @click="toggleDropdown" class="dropdown-button" ref="dropdownButton">
      ▼
      <!-- Downward-pointing arrow -->
    </button>
  </div>
</template>

<script>
import { querySuggestions } from '../mocks/index' // Import the list of query suggestions

export default {
  props: {
    query: {
      type: String,
      default: '' // Default query value
    }
  },
  data() {
    return {
      isOpen: false, // Keep track of whether the dropdown is open or closed
      queries: [], // The list of queries fetched from local storage
      mouseOutTimer: null // Timer for closing dropdown when mouse is out for 1 second
    }
  },
  created() {
    this.updateQueriesFromLocalStorage() // Fetch queries during component initialization
  },
  mounted() {
    // Listen for changes in local storage
    window.addEventListener('storage', this.handleStorageEvent)

    document.addEventListener('click', this.handleDocumentClick) // Close dropdown when clicking outside
  },

  beforeUnmount() {
    window.removeEventListener('storage', this.handleStorageEvent) // Clean up event listeners

    document.removeEventListener('click', this.handleDocumentClick)
  },
  methods: {
    updateQueriesFromLocalStorage() {
      // Fetch queries from local storage
      const storedQueries = localStorage.getItem('queries')
      if (storedQueries) {
        this.queries = JSON.parse(storedQueries) // Parse JSON data
      } else {
        this.queries = [] // No queries found
      }
    },
    handleStorageEvent(event) {
      // If the local storage key that changed is "queries", update the queries
      console.log(event)
      if (event.key === 'queries') {
        console.log('Queries updated from storage')
        this.updateQueriesFromLocalStorage() // Update queries in the component
      }
    },
    toggleDropdown() {
      this.isOpen = !this.isOpen
      if (this.isOpen) {
        this.createPortal() // Create the floating dropdown
      } else {
        this.removePortal() // Remove the floating dropdown
      }
    },
    createPortal() {
      this.portal = document.createElement('div') // Create a new div
      this.portal.className = 'dropdown-portal' // Assign class for styling
      document.body.appendChild(this.portal) // Append to document body

      const rect = this.$refs.dropdownButton.getBoundingClientRect() // Get the button's position
      this.portal.style.position = 'absolute'
      this.portal.style.top = `${rect.bottom}px` // Place below the button
      this.portal.style.left = `${rect.right}px` // Adjust to appear on the left side of the button

      this.portal.style.transform = 'translateX(-100%) translateY(4px)' // Translate to align with the left side

      if (this.queries.length > 0) {
        const historyList = this.generateHistoryList() // Populate the portal with the list
        this.portal.appendChild(historyList)
      }

      const suggestionList = this.generateSuggestedList() // Populate the portal with the list
      this.portal.appendChild(suggestionList)

      // Add mouse enter and leave event listeners for closing after 1 second
      this.portal.addEventListener('mouseleave', this.handleMouseLeave)
      this.portal.addEventListener('mouseenter', this.handleMouseEnter)

      // Attach click event listener to the portal (for event delegation)
      this.portal.addEventListener('click', this.handlePortalClick)
    },
    removePortal() {
      if (this.portal) {
        this.portal.removeEventListener('mouseleave', this.handleMouseLeave)
        this.portal.removeEventListener('click', this.handlePortalClick)
        document.body.removeChild(this.portal) // Remove from the document body
        this.portal = null // Reset the portal reference
      }
    },
    generateHistoryList() {
      const list = document.createElement('ul')
      list.className = 'dropdown-list'

      // Add a header for the history
      const header = document.createElement('p')
      header.textContent = 'History'
      header.style.fontWeight = 'bold' // Make the header bold
      list.appendChild(header)

      // Create numbered list items for queries
      this.queries.forEach((query, index) => {
        const listItem = document.createElement('li')
        listItem.textContent = `${index + 1} - ${query}` // Numbered item
        listItem.addEventListener('click', () => this.selectQuery(query))
        list.appendChild(listItem)
      })

      return list
    },
    generateSuggestedList() {
      const list = document.createElement('ul')
      list.className = 'dropdown-list'

      // Add a header for the history
      const header = document.createElement('p')
      header.textContent = 'Suggestions'
      header.style.fontWeight = 'bold' // Make the header bold
      list.appendChild(header)

      // Create numbered list items for queries
      querySuggestions.forEach((query, index) => {
        const listItem = document.createElement('li')
        listItem.textContent = `${index + 1} - ${query}` // Numbered item
        listItem.addEventListener('click', () => this.selectQuery(query))
        list.appendChild(listItem)
      })

      return list
    },
    handlePortalClick(event) {
      // Check if the click happened on a list item
      if (event.target.tagName.toLowerCase() === 'li' && event.target.textContent !== 'History') {
        const query = event.target.textContent.split(' - ')[1] // Extract the query text
        this.selectQuery(query) // Handle the selection
      }
    },
    handleDocumentClick(event) {
      if (
        this.isOpen &&
        !this.$refs.dropdownButton.contains(event.target) &&
        !this.portal.contains(event.target)
      ) {
        this.isOpen = false // Close the dropdown when clicking outside
        this.removePortal()
      }
    },
    handleMouseLeave() {
      this.mouseOutTimer = setTimeout(() => {
        this.removePortal() // Close the dropdown
        this.isOpen = false
      }, 1000) // Set timer to close after 1 second
    },
    handleMouseEnter() {
      clearTimeout(this.mouseOutTimer) // Clear the timer if the mouse re-enters
    },
    selectQuery(query) {
      this.$emit('update:query', query) // Emit the selected query
      this.isOpen = false // Close the dropdown
      this.removePortal() // Remove the portal
    }
  }
}
</script>

<style>
.dropdown {
  position: relative;
}

.dropdown-button {
  border-radius: 2px;
  border: 1px solid #2c5b4e;
  background-color: #36413e;
  color: white;
  cursor: pointer;
  outline: none;
  height: 39.5px;
  aspect-ratio: 1/1;
}

.dropdown-button:disabled {
  cursor: not-allowed;
  background-color: #36413e;
}

.dropdown-portal {
  z-index: 10000;
  background-color: #36413e;
  border: 1px solid #2c5b4e;
  border-radius: 2px;
  transform: translateX(-100%) translateY(4px);
}

.dropdown-list {
  list-style-type: none;
  padding: 0;
  color: white;

  max-width: 500px;
}

.dropdown-list li,
p {
  padding: 8px;
}

.dropdown-list li {
  cursor: pointer;

  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.dropdown-list li:hover {
  background-color: #2c5b4e;
}

.dropdown-list li:first-child {
  font-weight: bold;
}
</style>
