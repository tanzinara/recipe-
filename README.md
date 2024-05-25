#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

typedef struct Ingredient
{
    char I_name[30];
    int amount;
    char unit[20];

} ingre;

typedef struct Recipe
{
    char R_name[30];
    int total_ingrat_amount;
    ingre R_ingredient[10];
    char ins[100];
    int user_id;
    int recipe_id;
    float review;
    int total_review;
    char comment[20][50];
    int comment_index;
    int competition;

} recipe;

typedef struct User
{
    char Name[10];
    char pass[10];
    int user_ID;
} user;
char user_name[10];
int login_status = 0;
int user_id = 0;
////////Function Declaration/////////
int id_generator();
void clear();
void pause();
void popularRecipes();
void searchRecipes();
void searchByIngredients();
void signInSignOut();
void signin();
void signup();
void developerInformation();
void user_meanu();
void competition();
void see_score();
void vote();
void recipe_calculator();
void diet_plan();
void addrecipe(int ch);
void reviews();
void myrecipe();
void edit_and_save(recipe *data);
/////////////////////////////////////
int id_generator()
{
    srand(time(NULL));
    return rand() % 900000 + 100000;
}
void clear()
{
    system("cls");
}
void pause()
{
    fflush(stdin);
    printf("\nPress Enter To Continue.......");
    getchar();
}
void popularRecipes()
{
    clear();
    FILE *ptr = fopen("recipe.bin", "rb");
    if (ptr == NULL)
    {
        printf("Error Opening files\n");
    }
    recipe data;
    int i = 1;
    while (fread(&data, sizeof(recipe), 1, ptr))
    {
        if (data.comment_index > 3)
        {
            printf("[%d] Recipe Name: %s\n", i++, data.R_name);
        }
    }
    if (i == 1)
    {
        printf("Not Enough Data\n");
    }
    pause();
}
void searchRecipes()
{
    clear();
    char recipename[30];
    printf("Enter the recipe name:");
    scanf(" %[^\n]s", recipename);
    FILE *file = fopen("recipe.bin", "rb");
    if (file == NULL)
    {
        printf("Error opening file!\n");
        exit(1);
    }
    recipe add;
    int found = 0;
    while (fread(&add, sizeof(recipe), 1, file))
    {

        if (strcmp(recipename, add.R_name) == 0)
        {
            found = 1;
            printf("\nRecipe name %s", add.R_name);
            printf("\nRecipe Ingredients %d", add.total_ingrat_amount);
            for (int i = 0; i < add.total_ingrat_amount; i++)
            {
                printf("\nIngredients name %s", add.R_ingredient[i].I_name);
                printf("\nIngredients amount %d", add.R_ingredient[i].amount);
                printf(" %s\n", add.R_ingredient[i].unit);
            }
            printf(" Instruction \n%s\n", add.ins);
            break;
        }
    }
    if (!found)
    {
        fclose(file);
        printf("Recipe Not Found\n");
        pause();
        if (login_status)
        {
            user_meanu();
        }
        main();
    }
    fclose(file);
    if (login_status)
    {
        printf("Write A Comment:");
        scanf(" %[^\n]s", add.comment[add.comment_index]);
        add.comment_index += 1;
        edit_and_save(&add);
        pause();
        user_meanu();
    }
    pause();
    main();
}
void searchByIngredients()
{
    clear();
    char recipename[30];
    printf("Enter the recipe name:");
    scanf(" %[^\n]s", recipename);
    FILE *file = fopen("recipe.bin", "rb");
    if (file == NULL)
    {
        printf("Error opening file!\n");
        exit(1);
    }
    recipe add;
    int found = 0;
    while (fread(&add, sizeof(recipe), 1, file))
    {

        for (int i = 0; i < add.total_ingrat_amount; i++)
        {
            if (strcmp(recipename, add.R_ingredient[i].I_name) == 0)
            {
                found = 1;
                printf("\nRecipe name %s", add.R_name);
                printf("\nRecipe Ingredients %d", add.total_ingrat_amount);
                for (int j = 0; j < add.total_ingrat_amount; j++)
                {
                    printf("\nIngredients name %s", add.R_ingredient[j].I_name);
                    printf("\nIngredients amount %d", add.R_ingredient[j].amount);
                    printf(" %s\n", add.R_ingredient[j].unit);
                }
                printf(" Instruction \n%s\n", add.ins);
                break;
            }
        }
        if (found)
        {
            break;
        }
    }
    if (!found)
    {
        fclose(file);
        printf("Recipe Not Found\n");
        pause();
        if (login_status)
        {
            user_meanu();
        }
        main();
    }
    fclose(file);
    if (login_status)
    {
        printf("Write A Comment:");
        scanf(" %[^\n]s", add.comment[add.comment_index]);
        add.comment_index += 1;
        edit_and_save(&add);
        pause();
        user_meanu();
    }
    pause();
    main();
}
void signInSignOut()
{
    while (1)
    {
        clear();
        int a;
       printf("\t\t\t\t\t===================================\n");
    printf("\t\t\t\t\t=                                 =\n");
    printf("\t\t\t\t\t=           Welcome to            =\n");
    printf("\t\t\t\t\t=         Our Application         =\n");
    printf("\t\t\t\t\t=                                 =\n");
    printf("\t\t\t\t\t===================================\n");
    printf("\t\t\t\t\t=                                 =\n");
    printf("\t\t\t\t\t=          1. Sign In             =\n");
    printf("\t\t\t\t\t=          2. Sign Up             =\n");
    printf("\t\t\t\t\t=                                 =\n");
    printf("\t\t\t\t\t===================================\n");
    printf(">> ");
        scanf("%d", &a);
        switch (a)
        {
        case 1:
            signin();
            break;
        case 2:
            signup();
            break;
        default:
            printf("Wrong\n");
            pause();
            main();
            break;
        }
    }
}
void signin()
{
    clear();
    char name[10];
    printf("\nEnter your User name:");
    scanf(" %[^\n]s", name);
    char pass[10];
    printf("\nEnter Your pass:");
    scanf(" %[^\n]s", pass);
    FILE *file = fopen("users.bin", "rb"); // Open file in read binary mode
    if (file == NULL)
    {
        printf("Error opening file!\n");
        exit(1);
    }
    user person;
    int found = 0;
    while (fread(&person, sizeof(user), 1, file))
    {

        if ((strcmp(name, person.Name) == 0) && (strcmp(pass, person.pass) == 0))
        {
            found = 1;
            user_id = person.user_ID;
            break;
        }
    }

    fclose(file);
    if (!found)
    {
        printf("Not found\n");
        signInSignOut();
    }
    if (found)
    {
        strcpy(user_name, name);
        login_status = 1;
        user_meanu();
    }
}
void signup()
{
    clear();
    user person;
    printf("Enter Your User Name:");
    scanf(" %[^\n]s", person.Name);
    printf("\nEnter Your password:");
    scanf(" %[^\n]s", person.pass);
    person.user_ID = id_generator();
    FILE *file = fopen("users.bin", "ab");
    if (file == NULL)
    {
        printf("Error opening file!\n");
        exit(1);
    }

    fwrite(&person, sizeof(user), 1, file);

    fclose(file);
}

void developerInformation()
{
    printf("Developer Information...\n");
    // Code to display developer information goes here
}

int main()
{
    char choice;

    do
    {
        clear();
        printf("\n");
        printf("\t\t\t\t\t****************************************************\n");
    printf("\t\t\t\t\t****************************************************\n");
    printf("\t\t\t\t\t*                                                  *\n");
    printf("\t\t\t\t\t*         Welcome To Our Cooking World             *\n");
    printf("\t\t\t\t\t*                                                  *\n");
    printf("\t\t\t\t\t****************************************************\n");
    printf("\t\t\t\t\t*                                                  *\n");
    printf("\t\t\t\t\t*           1. Popular Recipes                     *\n");
    printf("\t\t\t\t\t*           2. Search Recipes                      *\n");
    printf("\t\t\t\t\t*           3. Recipe Calculator                   *\n");
    printf("\t\t\t\t\t*           4. SignIn/SignOut                      *\n");
    printf("\t\t\t\t\t*           5. Developer Information               *\n");
    printf("\t\t\t\t\t*           6. Competition                         *\n");
    printf("\t\t\t\t\t*           7. Exit                                *\n");
    printf("\t\t\t\t\t*                                                  *\n");
    printf("\t\t\t\t\t****************************************************\n");
    printf("\t\t\t\t\t****************************************************\n");
    printf("\n");
    printf("****************************************************\n");
    printf("* Enter your choice:                               *\n");
    printf("****************************************************\n");

        scanf(" %c", &choice);

        switch (choice)
        {
        case '1':
            popularRecipes();
            break;
        case '2':
            searchRecipes();
            break;
        case '3':
            recipe_calculator();
            break;
        case '4':
            signInSignOut();
            break;
        case '5':
            developerInformation();
            break;
        case '6':
            competition();
            break;
        case '7':
            printf("Exiting...\n");
            exit(0);
        default:
            printf("Invalid choice! Please enter a number between 1 and 7.\n");
        }
    } while (choice != '7');

    return 0;
}
void user_meanu()
{
    while (1)
    {
        system("cls");
        int choice;
        printf("\n");
    printf("\t\t\t\t\t###################################################\n");
    printf("\t\t\t\t\t#                                                 #\n");
    printf("\t\t\t\t\t#               \"Welcome to Recipe Hub\"           #\n");
    printf("\t\t\t\t\t#                                                 #\n");
    printf("\t\t\t\t\t###################################################\n");
    printf("\t\t\t\t\t#                                                 #\n");
    printf("\t\t\t\t\t#               1. Add Recipe                     #\n");
    printf("\t\t\t\t\t#               2. My Recipes                     #\n");
    printf("\t\t\t\t\t#               3. Search Recipe by Name          #\n");
    printf("\t\t\t\t\t#               4. Search Recipes by Ingredients  #\n");
    printf("\t\t\t\t\t#               5. My Reviews                     #\n");
    printf("\t\t\t\t\t#               6. My Diet Plan                   #\n");
    printf("\t\t\t\t\t#               7. Recipe Calculator              #\n");
    printf("\t\t\t\t\t#               8. Competition                    #\n");
    printf("\t\t\t\t\t#               9. Exit                           #\n");
    printf("\t\t\t\t\t#                                                 #\n");
    printf("\t\t\t\t\t###################################################\n");
    printf("\t\t\t\t\t#                                                 #\n");
    printf("\t\t\t\t\t# Enter your choice:                              #\n");
    printf("\t\t\t\t\t#                                                 #\n");
    printf("\t\t\t\t\t###################################################\n");
        scanf("%d", &choice);

        switch (choice)
        {
        case 1:
            addrecipe(0);
            break;
        case 2:
            myrecipe();
            break;
        case 3:
            searchRecipes();
            break;
        case 4:
            searchByIngredients();
            break;
        case 5:
            reviews();
            break;
        case 6:
            diet_plan();
            break;
        case 7:
            recipe_calculator();
            break;
        case 8:
            competition();
            break;
        case 9:
            login_status = 0;
            main();
            break;
        }
    }
}
void competition()
{
    if(!login_status)
    {
        printf("Login to View\n");
        pause();
        main();
    }
    clear();
    printf("[1] Join Competition  [2]Vote  [3] See My Score\n");
    int ch;
    scanf(" %d", &ch);
    switch (ch)
    {
    case 1:
        addrecipe(1);
        break;
    case 2:
        vote();
        break;
    case 3:
        see_score();
        break;
    default:
        break;
    }
}
void see_score()
{
    recipe data;
    FILE *ptr = fopen("recipe.bin", "rb");
    if (ptr == NULL)
    {
        perror("Error opening File");
    }
    int i = 1;

    while (fread(&data, sizeof(recipe), 1, ptr))
    {
        if (data.user_id == user_id && data.competition == 1)
        {
            printf("[%d] Recipe Name: %s\n", i++, data.R_name);
            printf("Review: %.2f \n", data.review / data.total_review);
        }
    }
    if (i == 1)
    {
        printf("Nothing to see here\n");
    }
    pause();
    user_meanu();
}
void vote()
{
    recipe data;
    FILE *ptr = fopen("recipe.bin", "rb");
    if (ptr == NULL)
    {
        perror("Error opening File");
    }
    int i=1;
    while (fread(&data, sizeof(recipe), 1, ptr))
    {
        if (data.competition == 1)
        {
            printf("[%d] Recipe Name: %s\n", i++, data.R_name);
            printf("\n[1] Give Rating [2] Skip  [3] Return\n>>");
            int ch;
            scanf(" %d", &ch);
            if (ch == 1)
            {
                printf("Give a rating  from 1 to 5\n");
                float num;
                scanf(" %f", &num);
                data.review += num;
                data.total_review += 1;
                edit_and_save(&data);
            }
            else if (ch == 2)
            {
                continue;
            }
            else
            {
                fclose(ptr);
                pause;
                user_meanu();
            }
        }
    }
    fclose(ptr);
    pause;
    user_meanu();
}
void recipe_calculator()
{
    clear();
    char recipename[30];
    printf("Enter the recipe name:");
    scanf(" %[^\n]s", recipename);
    printf("No. Of people to cook for:");
    int num;
    scanf(" %d", &num);
    FILE *file = fopen("recipe.bin", "rb");
    if (file == NULL)
    {
        printf("Error opening file!\n");
        exit(1);
    }
    recipe add;
    int found = 0;
    while (fread(&add, sizeof(recipe), 1, file))
    {

        if (strcmp(recipename, add.R_name) == 0)
        {
            found = 1;
            printf("\nRecipe name %s", add.R_name);
            printf("\nRecipe Ingredients %d", add.total_ingrat_amount);
            for (int i = 0; i < add.total_ingrat_amount; i++)
            {
                printf("\nIngredients name %s", add.R_ingredient[i].I_name);
                printf("\nIngredients amount %d", add.R_ingredient[i].amount * num);
                printf(" %s\n", add.R_ingredient[i].unit);
            }
            printf(" Instruction \n%s\n", add.ins);
            break;
        }
    }
    if (!found)
    {
        fclose(file);
        printf("Recipe Not Found\n");
        pause();
        if (login_status)
        {
            user_meanu();
        }
        main();
    }

    pause();
    user_meanu();
}
void diet_plan()
{

}
void addrecipe(int ch)
{
    clear();
    recipe add;
    printf("Recipe Name:");
    scanf(" %[^\n]s", add.R_name);
    printf("\nIngredient amount:");
    scanf(" %d", &add.total_ingrat_amount);
    for (int i = 0; i < add.total_ingrat_amount; i++)
    {
        printf("\n%d. Ingredient Name", i + 1);
        scanf(" %[^\n]s", add.R_ingredient[i].I_name);
        printf("\nIngredient amount:");
        scanf(" %d", &add.R_ingredient[i].amount);
        printf("\nIngredient unit");
        scanf(" %[^\n]s", add.R_ingredient[i].unit);
    }
    add.user_id = user_id;
    add.recipe_id = id_generator();
    add.comment_index = 0;
    add.review = 0.0;
    add.total_review = 0;
    add.competition = ch;
    printf("Enter full instruction:");
    scanf(" %[^\n]s", add.ins);

    FILE *file = fopen("recipe.bin", "ab");
    if (file == NULL)
    {
        printf("Error opening file!\n");
        exit(1);
    }

    fwrite(&add, sizeof(recipe), 1, file);

    fclose(file);
}
void reviews()
{
    clear();
    printf("My Recipes\n");
    recipe data;
    int i = 0;
    FILE *ptr = fopen("recipe.bin", "rb");
    if (ptr == NULL)
    {
        printf("Error Opening File\n");
    }
    while (fread(&data, sizeof(recipe), 1, ptr))
    {
        if (data.user_id == user_id)
        {
            printf("[%d] Recipe Name: %s\n", i++, data.R_name);
            printf("Comments\n");
            for (int i = 0; i < data.comment_index; i++)
            {
                printf("[%d] %s\n", i + 1, data.comment[i]);
            }
        }
    }
    if (!i)
    {
        printf("No Recipes Found\n");
    }
    pause();
    user_meanu();
}

void myrecipe()
{
    clear();
    printf("My Recipes\n");
    recipe data;
    int i = 0;
    FILE *ptr = fopen("recipe.bin", "rb");
    if (ptr == NULL)
    {
        printf("Error Opening File\n");
    }
    while (fread(&data, sizeof(recipe), 1, ptr))
    {
        if (data.user_id == user_id)
        {
            printf("[%d] Recipe Name: %s\n", i++, data.R_name);
        }
    }
    if (!i)
    {
        printf("No Recipes Found\n");
    }
    pause();
    user_meanu();
}
void edit_and_save(recipe *data)
{
    recipe temp;
    FILE *ptr = fopen("recipe.bin", "rb+");
    if (ptr == NULL)
    {
        perror("Error opening File");
        return;
    }

    int offset_index = 0;
    int found = 0;

    while (fread(&temp, sizeof(recipe), 1, ptr))
    {
        if (temp.recipe_id == data->recipe_id)
        {
            found = 1;
            break;
        }
        offset_index++;
    }

    if (!found)
    {
        printf("Recipe ID not found\n");
        fclose(ptr);
        return;
    }

    long offset = offset_index * sizeof(recipe);
    if (fseek(ptr, offset, SEEK_SET) != 0)
    {
        perror("Error seeking in file");
        fclose(ptr);
        return;
    }

    if (fwrite(data, sizeof(recipe), 1, ptr) != 1)
    {
        perror("Error writing file");
        fclose(ptr);
        return;
    }

    fclose(ptr);
}
