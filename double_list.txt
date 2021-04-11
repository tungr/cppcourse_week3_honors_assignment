#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#define LIST_SIZE 200

/*
 * Program that reorders a list of 200 random integers between 0-49 
 * and prints them out in rows of 5. Then removes duplicate 
 * integers and reprints with the new list. - Robert Tung
*/

typedef struct list{int data; struct list *next; struct list *prev;} list;

// Checks if list is empty by checking 
// if current pointer in list is NULL
int is_empty(const list *l) {return (l == NULL);}

// Creates new data node with 
// reference to next and prev nodes
list* create_list(int d)
{
    list* head = malloc(sizeof(list)); 
    head->data = d;
    head->next = NULL;
    head->prev = NULL;
    return head;
}

// Creates doubly-linked list (with next and prev)
list* add_to_front(int d, list* h)
{
    list* head = create_list(d);
    // Makes new head next of last head
    head->next = h;
    // Makes last head prev of new head
    if(h != NULL)
    {
        h->prev = head; 
    }
    return head;
}

// Adds array elements into list of size
list* array_to_list(int d[], int size)
{
    list* head = create_list(d[0]);
    for(int i = 1; i < size; i++)
    {
        head = add_to_front(d[i], head);
    }
    return head;
}

// Remove node from list
void remove_node(list** head, list* del)
{
    // Base case
    if (*head == NULL || del == NULL)
    {
        return;
    }

    // If head node needs to be removed
    if (*head == del)
    {
        *head = del->next;
    }

    // Reconnects next/prev if the last node
    // in the list doesn't need to be removed
    if (del->next != NULL)
    {
        del->next->prev = del->prev;
    }

    if (del->prev != NULL)
    {
        del->prev->next = del->next;
    }

    free(del);
}

// Remove duplicate data nodes
// Double-pointer for a list of heads
void remove_dupes(list** head)
{
    // If list is empty (should never happen)
    if (*head == NULL)
    {
        return;
    }

    struct list* current = *head;
    struct list* next;

    while(current->next != NULL)
    {
        if(current->data == current->next->data)
        {
            remove_node(head, current->next);
        }

        else
        {
            current = current->next;
        }
    }
}

// Swap positions of two values
void swap(int *x, int *y)
{
    int temp = *x;
    *x = *y;
    *y = temp;
}

// Backwards Sorting algorithm (to account for array_to_list printing in reverse order)
// If current element < next element
void bubbleSort(int h[], int size)
{
    for (int i = 0; i < size-1; i++)
    {
        for (int j = 0; j < size-i-1; j++)
        {
            if (h[j] < h[j+1])
            {
                swap(&h[j], &h[j+1]);
            }
        }
    }
}

// Print list until head returns NULL
void print_list(list *h, char *title)
{
    printf("%s\n", title);
    int i;
    while (h != NULL)
    {
        printf ("%d ", h->data);
        h = h->next;
        // Prints a new line every 5 elements
        if(++i % 5 == 0)
        {
            printf("\n");
        }
    }
}

// Runs the program
int main()
{
    list list_of_int;
    list* head = NULL;
    srand(time(NULL));
    int data[LIST_SIZE];

    for (int i = 0; i < LIST_SIZE; i++) 
    {
        data[i] = rand() % 50;
    }
    
    bubbleSort(data, LIST_SIZE);
    head = array_to_list(data, LIST_SIZE);
    print_list(head, "List of 200 random sorted elements");
    printf("\n");
    remove_dupes(&head);
    print_list(head, "List after removing duplicate elements");
    printf("\n");

    return 0;
}