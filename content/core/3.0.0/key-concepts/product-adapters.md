---
title: Product Adapters
description: Converting product sources into understandable products for Vendr, the eCommerce solution for Umbraco
---

The role of a Product Adapter in Vendr is to provide an interface between a product information source (which by default will be an Umbraco node) and convert it into a standardized format such that Vendr doesn't need to be tied to that source.

What this means for developers is that Product Adapters allow you to hook in alternative product information sources that may not be Umbraco node based. For example, you may hold your product information in a third party database table, and so a custom Product Adapter would provide a means for Vendr to interface with that custom data in the same way as it would the default Umbraco node data source.

## Example Product Adapter

An example of a Product Adapter would look something like this:

````csharp
public class MyCustomProductAdapter : IProductAdapter
{
    public IProductSnapshot GetProductSnapshot(string productReference, string languageIsoCode)
    {
        // Lookup a product by productReference and convert to IProductSnapshot
    }

    public IProductSnapshot GetProductSnapshot(string productReference, string productVariantReference, string languageIsoCode)
    {
        // Lookup a product by productVariantReference and convert to IProductSnapshot
    }

    public bool TryGetProductReference(Guid storeId, string sku, out string productReference, out string productVariantReference)
    {
        // Try lookup a product / variant reference by store + sku
    }
}

````

All Product Adapters implement the `IProductAdapter` interface which requires three method implementations. Two `GetProductSnapshot` methods which retrieve a Product Snapshot for either a product or product variant by reference parameters and a `TryGetProductReference` method which retrieves a product / variant reference for a product that belongs to a given `storeId` and has the given `sku`.

A Product Snapshot consists of the following properties in order to present a Product to Vendr in a standard way. 


````csharp
public interface IProductSnapshot
{
    // The unique reference for the product
    string ProductReference { get; }

    // The unique reference for the variant (if this is a variant snapshot)
    string ProductVariantReference { get; }

    // The unique SKU for this product / variant
    string Sku { get; }

    // The name of this product / variant
    string Name { get; }

    // The ID of the store this product / variant belongs to
    Guid StoreId { get; }

    // An optional Tax Class ID for this product / variant
    Guid? TaxClassId { get; }

    // Any properties exposed by this product / variant that should be copied to the orderline
    IDictionary<string, string> Properties { get; }

    // Any variant attributes for this product (if this is a variant snapshot)
    IEnumerable<AttributeCombination> Attributes { get; }

    // The available prices for this product / variant
    IEnumerable<ProductPrice> Prices { get; }

    // Flag indicating whether this product is a gift card product
    bool IsGiftCard { get; }
}

````

## Support editable carts

To support editable carts and to allow Vendr to be able to search for products / variants to add to a cart via the back office, Product Adapters can also implement 3 additional methods.

````csharp
public class MyCustomProductAdapter : ProductAdapterBase
{
    ... 

    public override PagedResult<IProductSummary> SearchProductSummaries(Guid storeId, string languageIsoCode, string searchTerm, long currentPage = 1, long itemsPerPage = 50)
    {
        // Search for products matching the given search term and convert to a IProductSummary
    }

    public override IEnumerable<Attribute> GetProductVariantAttributes(Guid storeId, string productReference, string languageIsoCode)
    {
        // Lookup the in use product attributes of a primary product
    }

    public override PagedResult<IProductVariantSummary> SearchProductVariantSummaries(Guid storeId, string productReference, string languageIsoCode, string searchTerm, IDictionary<string, IEnumerable<string>> attributes, long currentPage = 1, long itemsPerPage = 50)
    {
        // Search for product variants matching the given search term and/or the given attributes and convert to a IProductVariantSummary
    }
}

````

The `IProductSummary`, `Attribute` and `IProductVariantSummary` consists of the following properties in order to present a Product to Vendr in a standard way. 


````csharp
public interface IProductSnapshot
{
    // The unique reference for the product
    string Reference { get; }

    // The unique SKU for this product 
    string Sku { get; }

    // The name of this product 
    string Name { get; }

    // The available prices for this product 
    IEnumerable<ProductPrice> Prices { get; }

    // Flag indicating whether this product has variants
    bool HasVariants { get; }
}

public class Attribute 
{
    // The alias of the attribute
    public string Alias { get; }

    // The name of the attribute
    public string Name { get; }

    // The attribute values
    IEnumerable<AttributeValue> Values { get; }
}

public class AttributeValue
{
    // The alias of the attribute value
    public string Alias { get; }

    // The name of the attribute value
    public string Name { get; }

}

public interface IProductVariantSnapshot
{
    // The unique reference for the product variant
    string Reference { get; }

    // The unique SKU for this product variant
    string Sku { get; }

    // The name of this product variant
    string Name { get; }

    // The available prices for this product variant
    IEnumerable<ProductPrice> Prices { get; }

    // The collection of attribute alias pairs of this product variant
    IReadOnlyDictionary<string, string> Attributes { get; }
}

````

## Registering a Product Adapter

Product Adapters are [registered via the IVendrBuilder](../vendr-builder/#registering-dependencies) interface using the `AddUnique<IProductAdapter, TReplacementAdapter>()` method on the `Services` property where the `TReplacementAdapter` parameter is the Type of our custom Product Adapter implementation.

````csharp
public static class VendrBuilderExtensions
{
    public static IVendrBuilder AddMyServices(IVendrBuilder builder)
    {
        // Replacing the default Product Adapter implementation
        builder.Services.AddUnique<IProductAdapter, MyCustomProductAdapter>();

        // Return the builder to continue the chain
        return builder;
    }
}
````
