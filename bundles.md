# Bundles:

## Description:

We want to let the creators bundle their products and sell the bundle. 

### General Questions:
1. Where in the Gumroad app, website, analytics we want to distinguish between products and bundled products?
2. Do Gumroad backend uses a text search database? 
3. Do we want to have a different menu/page for creating a bundle or we want to adjust the create product page (e.g. with a drop down select)
4. Do we care if a product which is being disabled/deleted is still in the bundle?


### Technical Proposals:

1. Store `Bundles` in `Product` table and `Distingiush` them with a type name `is_bundle` :
    #### Assumptions:
    1. We have a `Product` entity/record.    
    2. We don't want to distingush between `Bundle` and `Product` for examples in (search, read bundles not products or vice versa)
    3. `Product` entity is stored in a **relational database** e.g Postgres. 

    ### Idea: 
    We will represent the `Bundle` concepts as `Products` but we will distinguish them from `not_bundled` products. We will also record what products are in this bundle. 

    ### Solution:
    - Step 1: Add a **bundle_type** field **discriminator** to the `Product` entity/table/record in order to be able to represent `Bundle` records. 
    - Step 2: Add a **products_in_bundle** field/column to the `Product` entity/table/record in order to record what products contain in a bundle.

    ### Affects:
    1. **Database**:
    Add `bundle_type` and `products_in_bundle` fields to the Product table in database.
    2. **Migration**:
        - Set the `bundle_type` value to `not_bundled` for already existing products. 
        - Set the `products_in_bundle` value to `null` for already existing products.   
        - If there is a text search database like Elasticsearch we need to update those records as well.      
    3. **Backend**: 
        1. Create:  
            - Adjust create `product` API to include `bundle_type` and `products_in_bundle` from frontend app.
            **Note**: `bundle_type` values must be either `bundle` or `product`.
            **Note**: `products_in_bundle` should be a list of product_ids. 
            **Note** In case we want to make sure that products belongs to the same creator, we need to check them before persisting them.  
        2. Read:
            - Make sure to include `bundle_type` and `products_in_bundle` in the read result.
        3. Update: 
            Refer to `Create`
        4. Delete:
            - If the answer to assumption #4 is yes, we need to make sure that we do not disable/delete a product that is currently in a bundle. 
            No changes 
    4. **Frontend**
        1. Show Product Page:             
            - Frontend needs to list the products in the bundle in case the loaded product is a bundled product. 
            - In case `product.bundle_type` is `bundle` Frontend will get the `product` information for every product in the bundle and show them as a list in the page. 
        2. Frontend should let the creators Add bundles. 
            - option 1: Create a separate menu and create a separate page for `bundle` creation. We need to make sure we get all the product information such as description, price, ... along with products in bundle. 
            Creators will see a list of products and somehow choose what they want to include in the bundle.
            Frontend can show a list of creator's product or enable them to search their products. 
            - option 2: Change the create product page so that the creator can choose if this product is a bundle and enable them to choose between their product. 
        
        



         
    

2. `Bundle` entity:
    ### Idea:
    We will have a bundle entity along with the product entity. Bundles can have a many to many relationship with Products.  

    ### Solution Description:
    As Bundles are a collection of products we
