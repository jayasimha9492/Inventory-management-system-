#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_PRODUCTS 100

// Product structure
typedef struct {
    int id;
    char name[50];
    int quantity;
    float price;
} Product;

// Sold item node structure
typedef struct SoldItem {
    int productId;
    int quantitySold;
    float totalPrice;
    struct SoldItem* next;
} SoldItem;

Product catalog[MAX_PRODUCTS];
int productCount = 0;
SoldItem* salesHead = NULL;

// Function declarations
void addProduct();
void listProducts();
void sellProduct();
void addSale(int productId, int quantitySold, float totalPrice);
void viewSalesHistory();

int main() {
    int choice;

    do {
        printf("\nInventory Management System\n");
        printf("1. Add Product\n");
        printf("2. View Catalog\n");
        printf("3. Sell Product\n");
        printf("4. View Sales History\n");
        printf("5. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addProduct(); break;
            case 2: listProducts(); break;
            case 3: sellProduct(); break;
            case 4: viewSalesHistory(); break;
            case 5: printf("Exiting...\n"); break;
            default: printf("Invalid choice.\n");
        }
    } while (choice != 5);

    return 0;
}

void addProduct() {
    if (productCount >= MAX_PRODUCTS) {
        printf("Catalog is full.\n");
        return;
    }

    Product p;
    p.id = productCount + 1;

    printf("Enter product name: ");
    scanf("%s", p.name);

    printf("Enter quantity: ");
    scanf("%d", &p.quantity);

    printf("Enter price: ");
    scanf("%f", &p.price);

    catalog[productCount++] = p;
    printf("Product added with ID %d.\n", p.id);
}

void listProducts() {
    printf("\nProduct Catalog:\n");
    for (int i = 0; i < productCount; i++) {
        printf("ID: %d | Name: %s | Quantity: %d | Price: %.2f\n",
               catalog[i].id, catalog[i].name, catalog[i].quantity, catalog[i].price);
    }
}

void sellProduct() {
    int id, qty;
    printf("Enter product ID to sell: ");
    scanf("%d", &id);

    if (id <= 0 || id > productCount) {
        printf("Invalid product ID.\n");
        return;
    }

    Product* p = &catalog[id - 1];
    printf("Enter quantity to sell: ");
    scanf("%d", &qty);

    if (qty > p->quantity) {
        printf("Not enough stock.\n");
        return;
    }

    p->quantity -= qty;
    float total = qty * p->price;
    addSale(p->id, qty, total);
    printf("Sold %d units of %s for %.2f\n", qty, p->name, total);
}

void addSale(int productId, int quantitySold, float totalPrice) {
    SoldItem* newSale = (SoldItem*)malloc(sizeof(SoldItem));
    newSale->productId = productId;
    newSale->quantitySold = quantitySold;
    newSale->totalPrice = totalPrice;
    newSale->next = salesHead;
    salesHead = newSale;
}

void viewSalesHistory() {
    SoldItem* current = salesHead;
    printf("\nSales History:\n");

    if (!current) {
        printf("No sales recorded.\n");
        return;
    }

    while (current != NULL) {
        Product* p = &catalog[current->productId - 1];
        printf("Product: %s | Quantity Sold: %d | Total Price: %.2f\n",
               p->name, current->quantitySold, current->totalPrice);
        current = current->next;
    }
}
