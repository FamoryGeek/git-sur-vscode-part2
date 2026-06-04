package com.app.dgpn.model.entity;


import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "adresses")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Adresse {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(nullable = false, length = 200)
    private String rue;

    @Column(length = 100)
    private String quartier;

    @Column(nullable = false, length = 100)
    private String commune;

    @Column(nullable = false, length = 100)
    private String prefecture;

    @Column(nullable = false, length = 100)
    private String region;

    @Column(length = 20)
    private String codePostal;

    @Column(nullable = false, length = 100)
    private String ville;

    @Column(nullable = false, length = 100)
    private String pays;

    @Column(name = "est_principale", nullable = false)
    private Boolean estPrincipale = true;

    @Column(columnDefinition = "TEXT")
    private String complementAdresse;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToOne(mappedBy = "adresse")
    private Utilisateur utilisateur;

    @OneToOne(mappedBy = "adresse")
    private PointContact pointContact;
}

package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.ActionAudit;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.JdbcTypeCode;
import org.hibernate.type.SqlTypes;

import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "audit_logs")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class AuditLog {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @ManyToOne
    @JoinColumn(name = "utilisateur_id")
    private Utilisateur utilisateur;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 50)
    private ActionAudit action;

    @Column(name = "table_name", nullable = false, length = 100)
    private String tableName;

    @Column(name = "record_id")
    private UUID recordId;

    /** JSON (PostgreSQL jsonb) : ne pas utiliser columnDefinition JSONB + String seul (bind varchar → erreur PG). */
    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "anciennes_valeurs")
    private String anciennesValeurs;

    @JdbcTypeCode(SqlTypes.JSON)
    @Column(name = "nouvelles_valeurs")
    private String nouvellesValeurs;

    @Column(name = "adresse_ip", length = 45)
    private String adresseIp;

    @Column(name = "user_agent", columnDefinition = "TEXT")
    private String userAgent;

    @Column(columnDefinition = "TEXT")
    private String description;

    @CreationTimestamp
    @Column(name = "date_action", nullable = false, updatable = false)
    private LocalDateTime dateAction;
}

package com.app.dgpn.model.entity;

import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;

import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "champs_rejetes")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class ChampRejete {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "pre_demande_id", nullable = false)
    private PreDemande preDemande;

    @Column(name = "nom_champ", nullable = false)
    private String nomChamp;
    // Ex: "nomDemandeur", "photo", "dateNaissanceDemandeur"

    @Column(name = "libelle_champ", nullable = false)
    private String libelleChamp;
    // Ex: "Nom du demandeur", "Photo d'identité"

    @Column(name = "motif_rejet", nullable = false, columnDefinition = "TEXT")
    private String motifRejet;

    @Column(name = "corrige", nullable = false)
    private Boolean corrige = false;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "validateur_id")
    private Utilisateur validateur;
}

package com.app.dgpn.model.entity;


import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "configuration_systeme")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class ConfigurationSysteme {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(unique = true, nullable = false, length = 100)
    private String cle;

    @Column(nullable = false, columnDefinition = "TEXT")
    private String valeur;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(name = "type_valeur", length = 50)
    private String typeValeur;

    @Column(nullable = false)
    private Boolean modifiable = true;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @Column(name = "est_actif", nullable = false)
    private Boolean estActif = true;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @ManyToOne
    @JoinColumn(name = "modifie_par_id")
    private Utilisateur modifiePar;
}

package com.app.dgpn.model.entity;


import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.DayOfWeek;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "creneaux_horaires")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class CreneauHoraire {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @ManyToOne(optional = false)
    @JoinColumn(name = "point_contact_id", nullable = false)
    private PointContact pointContact;

    @Column(nullable = false)
    private LocalDate date;

    @Column(name = "heure_debut", nullable = false)
    private LocalTime heureDebut;

    @Column(name = "heure_fin", nullable = false)
    private LocalTime heureFin;

    @Column(name = "capacite_max", nullable = false)
    private Integer capaciteMax;

    @Column(name = "places_reservees", nullable = false)
    private Integer placesReservees = 0;

    @Enumerated(EnumType.STRING)
    @Column(name = "jour_semaine", nullable = false)
    private DayOfWeek jourSemaine;

    @Column(name = "est_actif", nullable = false)
    private Boolean estActif = true;

    @Column(nullable = false)
    private Boolean actif = true;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "creneau")
    private Set<RendezVous> rendezVous = new HashSet<>();
}


package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.StatutDemande;
import com.app.dgpn.model.enums.TypeDemande;
import com.app.dgpn.model.enums.TypeDocument;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "demandes")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Demande {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(name = "numero_demande", unique = true, nullable = false, length = 50)
    private String numeroDemande;

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "pre_demande_id", nullable = false)
    private PreDemande preDemande;

    @ManyToOne(optional = false, fetch = FetchType.LAZY)
    @JoinColumn(name = "statut_id", nullable = false)
    private StatutDemandeEntity statut;

    @Enumerated(EnumType.STRING)
    @Column(name = "statut_actuel", nullable = false, length = 30)
    private StatutDemande statutActuel;

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "rendez_vous_id")
    private RendezVous rendezVous;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "utilisateur_id")
    private Utilisateur utilisateur;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "point_contact_traitement_id")
    private PointContact pointContactTraitement;

    @Column(name = "nom_demandeur", nullable = false, length = 100)
    private String nomDemandeur;

    @Column(name = "prenom_demandeur", nullable = false, length = 100)
    private String prenomDemandeur;

    @Column(name = "telephone_demandeur", nullable = false, length = 20)
    private String telephoneDemandeur;

    @Column(name = "nin_demandeur", length = 20)
    private String ninDemandeur;

    @Enumerated(EnumType.STRING)
    @Column(name = "type_document", nullable = false, length = 20)
    private TypeDocument typeDocument;

    @Enumerated(EnumType.STRING)
    @Column(name = "type_demande", nullable = false, length = 30)
    private TypeDemande typeDemande;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "agent_traitement_id")
    private Utilisateur agentTraitement;

    @Column(name = "date_depot")
    private LocalDate dateDepot;

    @Column(name = "date_traitement_estimee")
    private LocalDate dateTraitementEstimee;

    @Column(name = "date_traitement_reelle")
    private LocalDate dateTraitementReelle;

    @Column(name = "observations", columnDefinition = "TEXT")
    private String observations;

    @Column(name = "motif_rejet", columnDefinition = "TEXT")
    private String motifRejet;

    @Column(name = "localisation_physique", length = 200)
    private String localisationPhysique;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "demande", cascade = CascadeType.ALL)
    private Set<SuiviDemande> suivis = new HashSet<>();

    @OneToMany(mappedBy = "demande")
    private Set<PieceJointe> piecesJointes = new HashSet<>();

    @OneToOne(mappedBy = "demande", fetch = FetchType.LAZY)
    private TitreDelivre titreDelivre;
}



package com.app.dgpn.model.entity;


import com.app.dgpn.model.enums.CategorieDocument;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "documents_requis")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class DocumentRequis {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @ManyToOne(optional = false, fetch = FetchType.LAZY)
    @JoinColumn(name = "type_document_id", nullable = false)
    private TypeDocumentEntity typeDocument;

    @ManyToOne(optional = false, fetch = FetchType.LAZY)
    @JoinColumn(name = "type_demande_id", nullable = false)
    private TypeDemandeEntity typeDemande;

    @Column(nullable = false, length = 200)
    private String nom;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Enumerated(EnumType.STRING)
    @Column(name = "categorie_document", nullable = false, length = 50)
    private CategorieDocument categorieDocument;

    @Column(name = "est_obligatoire", nullable = false)
    private Boolean estObligatoire = true;

    @Column(name = "formats_acceptes", columnDefinition = "TEXT[]")
    private String[] formatsAcceptes;

    @Column(name = "taille_max_mo")
    private Integer tailleMaxMo;

    @Column(name = "pour_mineur", nullable = false)
    private Boolean pourMineur = false;

    @Column(name = "pour_naturalise", nullable = false)
    private Boolean pourNaturalise = false;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;
}


package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.ModePaiement;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "modes_paiement")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class ModePaiementEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Enumerated(EnumType.STRING)
    @Column(unique = true, nullable = false, length = 50)
    private ModePaiement code;

    @Column(nullable = false, length = 200)
    private String libelle;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(name = "frais_fixe", precision = 10, scale = 2)
    private BigDecimal fraisFixe;

    @Column(name = "frais_pourcentage", precision = 5, scale = 2)
    private BigDecimal fraisPourcentage;

    @Column(nullable = false)
    private Boolean actif = true;

    @Column(name = "necessite_telephone", nullable = false)
    private Boolean necessiteTelephone = false;

    @Column(name = "necessite_compte_bancaire", nullable = false)
    private Boolean necessiteCompteBancaire = false;

    @Column(name = "ordre_affichage")
    private Integer ordreAffichage;

    @Column(name = "icone", length = 255)
    private String icone;

    @Column(name = "couleur", length = 50)
    private String couleur;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;
}

package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.ModePaiement;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "modes_paiement")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class ModePaiementEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Enumerated(EnumType.STRING)
    @Column(unique = true, nullable = false, length = 50)
    private ModePaiement code;

    @Column(nullable = false, length = 200)
    private String libelle;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(name = "frais_fixe", precision = 10, scale = 2)
    private BigDecimal fraisFixe;

    @Column(name = "frais_pourcentage", precision = 5, scale = 2)
    private BigDecimal fraisPourcentage;

    @Column(nullable = false)
    private Boolean actif = true;

    @Column(name = "necessite_telephone", nullable = false)
    private Boolean necessiteTelephone = false;

    @Column(name = "necessite_compte_bancaire", nullable = false)
    private Boolean necessiteCompteBancaire = false;

    @Column(name = "ordre_affichage")
    private Integer ordreAffichage;

    @Column(name = "icone", length = 255)
    private String icone;

    @Column(name = "couleur", length = 50)
    private String couleur;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;
}


package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.CategoriePermission;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "permissions")
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString(exclude = {"roles"})
@EqualsAndHashCode(exclude = {"roles"})
public class Permission {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(unique = true, nullable = false, length = 100)
    private String code; // Ex: "DEMANDE_CREATE", "UTILISATEUR_DELETE"

    @Column(nullable = false, length = 200)
    private String nom; // Ex: "Créer une demande"

    @Column(columnDefinition = "TEXT")
    private String description;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 50)
    private CategoriePermission categorie;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @ManyToMany(mappedBy = "permissions")
    private Set<Role> roles = new HashSet<>();
}


package com.app.dgpn.model.entity;


import com.app.dgpn.model.enums.FormatDocument;
import com.app.dgpn.model.enums.StatutValidation;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "pieces_jointes")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class PieceJointe {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "pre_demande_id")
    private PreDemande preDemande;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "demande_id")
    private Demande demande;

    @ManyToOne(optional = false, fetch = FetchType.LAZY)
    @JoinColumn(name = "document_requis_id", nullable = false)
    private DocumentRequis documentRequis;

    @Column(name = "nom_fichier", nullable = false, length = 255)
    private String nomFichier;

    @Column(name = "chemin_fichier", nullable = false, length = 500)
    private String cheminFichier;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 10)
    private FormatDocument format;

    @Column(name = "taille_mo", nullable = false)
    private Double tailleMo;

    @Enumerated(EnumType.STRING)
    @Column(name = "statut", nullable = false, length = 20)
    private StatutValidation statutValidation;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "valide_par_agent_id")
    private Utilisateur valideParAgent;


    @Column(name = "date_validation")
    private LocalDateTime dateValidation;

    @Column(name = "commentaire_validation", columnDefinition = "TEXT")
    private String commentaireValidation;

    @CreationTimestamp
    @Column(name = "date_upload", nullable = false, updatable = false)
    private LocalDateTime dateUpload;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;
}


package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.TypePointContact;
import com.app.dgpn.model.enums.StatutPointContact;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "points_contact")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class PointContact {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(nullable = false, length = 200)
    private String nom;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 50)
    private TypePointContact type;

    @Column(name = "code_unique", unique = true, length = 50)
    private String codeUnique;

    @Column(unique = true, length = 50)
    private String code;

    @Column(nullable = false, length = 20)
    private String telephone;

    @Column(length = 255)
    private String email;

    @Column(length = 100)
    private String ville;

    @Column
    private Double latitude;

    @Column
    private Double longitude;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private StatutPointContact statut = StatutPointContact.ACTIF;

    @Column(nullable = false)
    private Boolean actif = true;

    @Column(name = "capacite_journaliere", nullable = false)
    private Integer capaciteJournaliere;

    /** Nombre de guichets ouverts (paramètre local PDC). */
    @Column(name = "nombre_guichets")
    private Integer nombreGuichets;

    /** Durée moyenne d’un passage citoyen (minutes). */
    @Column(name = "duree_passage_minutes")
    private Integer dureePassageMinutes;

    @ElementCollection(fetch = FetchType.LAZY)
    @CollectionTable(name = "points_contact_dates_fermeture", joinColumns = @JoinColumn(name = "point_contact_id"))
    @Column(name = "date_fermeture", nullable = false)
    private Set<LocalDate> datesFermetureExceptionnelle = new HashSet<>();

    // Stocké comme texte simple pour éviter les problèmes de typage JSONB côté PostgreSQL
    @Column(name = "horaires_ouverture", columnDefinition = "TEXT")
    private String horairesOuverture;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(columnDefinition = "TEXT[]")
    private String[] equipements;

    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    @JoinColumn(name = "adresse_id", nullable = false)
    private Adresse adresse;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "pointContact")
    private Set<Utilisateur> utilisateurs = new HashSet<>();

    @OneToMany(mappedBy = "pointContact", cascade = CascadeType.ALL)
    private Set<CreneauHoraire> creneaux = new HashSet<>();
}

package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.TypeDocument;
import com.app.dgpn.model.enums.TypeDemande;
import com.app.dgpn.model.enums.ProfilPasseport;
import com.app.dgpn.model.enums.TypePasseport;
import com.app.dgpn.model.enums.StatutPreDemande;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.ArrayList;
import java.util.List;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "pre_demandes")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class PreDemande {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(name = "numero_pre_demande", unique = true, nullable = false, length = 50)
    private String numeroPreDemande;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "utilisateur_id")
    private Utilisateur utilisateur;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "cree_par_agent_id")
    private Utilisateur creePar;

    @ManyToOne(optional = false, fetch = FetchType.LAZY)
    @JoinColumn(name = "type_document_id", nullable = false)
    private TypeDocumentEntity typeDocument;

    @ManyToOne(optional = false, fetch = FetchType.LAZY)
    @JoinColumn(name = "type_demande_id", nullable = false)
    private TypeDemandeEntity typeDemande;

    // Informations du demandeur
    @Column(name = "nom_demandeur", nullable = false, length = 100)
    private String nomDemandeur;

    @Column(name = "prenom_demandeur", nullable = false, length = 100)
    private String prenomDemandeur;

    @Column(name = "date_naissance_demandeur", nullable = false)
    private LocalDate dateNaissanceDemandeur;

    @Column(name = "lieu_naissance_demandeur", length = 200)
    private String lieuNaissanceDemandeur;

    @Column(name = "sexe_demandeur", length = 1)
    private String sexeDemandeur;

    @Column(name = "telephone_demandeur", nullable = false, length = 20)
    private String telephoneDemandeur;

    @Column(name = "email_demandeur", length = 255)
    private String emailDemandeur;

    @Column(name = "adresse_demandeur", columnDefinition = "TEXT")
    private String adresseDemandeur;

    @Column(name = "nin_demandeur", length = 20)
    private String ninDemandeur;

    @Column(name = "profession_demandeur", length = 100)
    private String professionDemandeur;

    @Column(name = "nationalite_demandeur", length = 100)
    private String nationaliteDemandeur;

    // Caractéristiques physiques et personnelles
    @Column(name = "statut_matrimonial", length = 50)
    private String statutMatrimonial; // Célibataire, Marié(e), Divorcé(e), Veuf(ve)

    @Column(name = "taille_cm")
    private Integer tailleCm; // Taille en centimètres

    @Column(name = "teint", length = 50)
    private String teint; // Clair, Moyen, Foncé

    @Column(name = "signe_particulier", length = 500)
    private String signeParticulier; // Signes particuliers (cicatrices, tatouages, etc.)

    @Column(name = "couleur_cheveux", length = 50)
    private String couleurCheveux; // Noir, Brun, Blond, Roux, etc.

    @Column(name = "couleur_yeux", length = 50)
    private String couleurYeux; // Noir, Marron, Bleu, Vert, etc.

    @Column(name = "nom_pere", length = 200)
    private String nomPere;

    @Column(name = "prenom_pere", length = 200)
    private String prenomPere;

    @Column(name = "nom_mere", length = 200)
    private String nomMere;

    @Column(name = "prenom_mere", length = 200)
    private String prenomMere;

    // Catégorie spécifique du passeport (Ordinaire / Diplomatique / De Service)
    @Enumerated(EnumType.STRING)
    @Column(name = "type_passeport", length = 30)
    private TypePasseport typePasseport;

    // Chemin du fichier de la photo d'identité (upload côté serveur)
    @Column(name = "path_photo", length = 500)
    private String pathPhoto;

    // Nom original du fichier photo (pour affichage UI)
    @Column(name = "nom_fichier_photo", length = 255)
    private String nomFichierPhoto;

    // Profil du passeport côté demandeur (nationaux / étranger adopté / naturalisés)
    @Enumerated(EnumType.STRING)
    @Column(name = "profil_passeport", length = 30)
    private ProfilPasseport profilPasseport;

    // Chemin du fichier d'autorisation parentale légalisée (passeport uniquement, mineurs)
    @Column(name = "path_autorisation_parentale", length = 500)
    private String pathAutorisationParentale;

    // Nom original du fichier autorisation parentale (pour affichage UI)
    @Column(name = "nom_fichier_autorisation_parentale", length = 255)
    private String nomFichierAutorisationParentale;

    // Champs spécifiques mineurs
    @Column(name = "est_mineur", nullable = false)
    private Boolean estMineur = false;

    @Column(name = "nom_tuteur", length = 200)
    private String nomTuteur;

    @Column(name = "prenom_tuteur", length = 200)
    private String prenomTuteur;

    @Column(name = "telephone_tuteur", length = 20)
    private String telephoneTuteur;

    // Champs spécifiques naturalisés
    @Column(name = "est_naturalise", nullable = false)
    private Boolean estNaturalise = false;

    @Column(name = "numero_decret_naturalisation", length = 100)
    private String numeroDecretNaturalisation;

    @Column(name = "date_naturalisation")
    private LocalDate dateNaturalisation;

    @Enumerated(EnumType.STRING)
    @Column(name = "statut", nullable = false, length = 30)
    private StatutPreDemande statut = StatutPreDemande.BROUILLON;

    @Column(name = "statut_modifie_par_id")
    private UUID statutModifieParId;

    @Column(name = "statut_modifie_le")
    private LocalDateTime statutModifieLe;

    @Column(name = "qr_code", columnDefinition = "TEXT")
    private String qrCode;

    @Column(name = "date_expiration")
    private LocalDate dateExpiration;

    @Column(name = "adresse_ip_creation", length = 45)
    private String adresseIpCreation;

    @Column(name = "user_agent", columnDefinition = "TEXT")
    private String userAgent;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @Column(name = "commentaire_rejet", columnDefinition = "TEXT")
    private String commentaireRejet;

    @OneToMany(mappedBy = "preDemande", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ChampRejete> champsRejetes = new ArrayList<>();

    @OneToMany(mappedBy = "preDemande")
    private Set<RendezVous> rendezVous = new HashSet<>();

    @OneToMany(mappedBy = "preDemande")
    private Set<PieceJointe> piecesJointes = new HashSet<>();

    @OneToMany(mappedBy = "preDemande")
    private Set<TransactionPaiement> transactions = new HashSet<>();

    @OneToOne(mappedBy = "preDemande", fetch = FetchType.LAZY)
    private Demande demande;
}

package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.ActionPreDemandeHistorique;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;

import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "pre_demande_historique")
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class PreDemandeHistorique {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "pre_demande_id", nullable = false)
    private PreDemande preDemande;

    @Enumerated(EnumType.STRING)
    @Column(name = "action", nullable = false, length = 50)
    private ActionPreDemandeHistorique action;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "agent_id")
    private Utilisateur agent;

    @Column(name = "motif_rejet", columnDefinition = "TEXT")
    private String motifRejet;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;
}


package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.StatutRendezVous;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.util.UUID;

@Entity
@Table(name = "rendez_vous")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class RendezVous {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @ManyToOne
    @JoinColumn(name = "utilisateur_id")
    private Utilisateur utilisateur;

    @ManyToOne(optional = false)
    @JoinColumn(name = "pre_demande_id", nullable = false)
    private PreDemande preDemande;

    @ManyToOne(optional = false)
    @JoinColumn(name = "creneau_id", nullable = false)
    private CreneauHoraire creneau;

    @ManyToOne(optional = false)
    @JoinColumn(name = "point_contact_id", nullable = false)
    private PointContact pointContact;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private StatutRendezVous statut;

    @Column(name = "date_rendez_vous", nullable = false)
    private LocalDateTime dateRendezVous;

    @Column(name="heure_debut", nullable=false)
    private LocalTime heureDebut;

    @Column(name = "heure_arrivee")
    private LocalDateTime heureArrivee;

    @Column(name = "motif_annulation", columnDefinition = "TEXT")
    private String motifAnnulation;

    @Column(name = "notification_envoyee", nullable = false)
    private Boolean notificationEnvoyee = false;

    @Column(name = "rappel_envoye", nullable = false)
    private Boolean rappelEnvoye = false;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;
}

package com.app.dgpn.model.entity;


import com.app.dgpn.model.enums.TypeRole;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;
import java.util.stream.Collectors;

@Entity
@Table(name = "roles")
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString(exclude = {"utilisateurRoles"})
@EqualsAndHashCode(exclude = {"utilisateurRoles"})
public class Role {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Enumerated(EnumType.STRING)
    @Column(unique = true, nullable = false, length = 50)
    private TypeRole nom;

    @Column(columnDefinition = "TEXT")
    private String description;

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
            name = "roles_permissions",
            joinColumns = @JoinColumn(name = "role_id"),
            inverseJoinColumns = @JoinColumn(name = "permission_id")
    )
    private Set<Permission> permissions = new HashSet<>();

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "role")
    private Set<UtilisateurRole> utilisateurRoles = new HashSet<>();

    // Méthode utilitaire pour obtenir les codes de permissions
    public Set<String> getPermissionCodes() {
        return permissions.stream()
                .map(Permission::getCode)
                .collect(Collectors.toSet());
    }
}

package com.app.dgpn.model.entity;


import com.app.dgpn.model.enums.StatutDemande;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "statuts_demande")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class StatutDemandeEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Enumerated(EnumType.STRING)
    @Column(unique = true, nullable = false, length = 50)
    private StatutDemande nom;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(name = "ordre_affichage", nullable = false)
    private Integer ordreAffichage;

    @Column(nullable = false)
    private Boolean actif = true;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "statut")
    private Set<Demande> demandes = new HashSet<>();
}

package com.app.dgpn.model.entity;


import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;

import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "suivi_demandes")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class SuiviDemande {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @ManyToOne(optional = false)
    @JoinColumn(name = "demande_id", nullable = false)
    private Demande demande;

    @ManyToOne(optional = false)
    @JoinColumn(name = "statut_id", nullable = false)
    private StatutDemandeEntity statut;

    @ManyToOne
    @JoinColumn(name = "agent_id")
    private Utilisateur responsableId;

    @Column(columnDefinition = "TEXT")
    private String commentaire;

    @Column(name = "localisation_physique", length = 200)
    private String localisationPhysique;

    @CreationTimestamp
    @Column(name = "date_changement", nullable = false, updatable = false)
    private LocalDateTime dateChangement;
}
package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.StatutTitre;
import com.app.dgpn.model.enums.TypeDocument;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "titres_delivres")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class TitreDelivre {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(name = "numero_titre", unique = true, nullable = false, length = 50)
    private String numeroTitre;

    @Enumerated(EnumType.STRING)
    @Column(name = "type_titre", nullable = false, length = 20)
    private TypeDocument typeTitre;

    @OneToOne
    @JoinColumn(name = "demande_id", nullable = false)
    private Demande demande;

    @Column(name = "nin_titulaire", length = 20)
    private String ninTitulaire;

    @Column(name = "telephone_titulaire", length = 20)
    private String telephoneTitulaire;

    @Column(name = "date_delivrance", nullable = false)
    private LocalDate dateDelivrance;

    @Column(name = "date_expiration", nullable = false)
    private LocalDate dateExpiration;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private StatutTitre statut;

    @Column(name = "lieu_emission", length = 200)
    private String lieuEmission;

    @ManyToOne
    @JoinColumn(name = "delivre_par_agent_id")
    private Utilisateur delivreParAgent;

    @Column(name = "date_retrait")
    private LocalDate dateRetrait;

    @Column(name = "signature_beneficiaire", length = 500)
    private String signatureBeneficiaire;

    @Column(columnDefinition = "TEXT")
    private String observations;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;
}
package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.ModePaiement;
import com.app.dgpn.model.enums.StatutPaiement;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.UUID;

@Entity
@Table(name = "transactions_paiement")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class TransactionPaiement {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(name = "numero_transaction", unique = true, nullable = false, length = 100)
    private String numeroTransaction;

    @ManyToOne(optional = false)
    @JoinColumn(name = "pre_demande_id", nullable = false)
    private PreDemande preDemande;

    @Column(nullable = false, precision = 10, scale = 2)
    private BigDecimal montant;

    @Enumerated(EnumType.STRING)
    @Column(name = "mode_paiement", nullable = false, length = 30)
    private ModePaiement modePaiement;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private StatutPaiement statut;

    @Column(name = "reference_externe", length = 200)
    private String referenceExterne;

    @Column(name = "paye_dunuya_transaction_id", length = 200)
    private String payeDunuyaTransactionId;

    @Column(name = "paye_dunuya_status", length = 50)
    private String payeDunuyaStatus;

    @Column(name = "numero_telephone_paiement", length = 20)
    private String numeroTelephonePaiement;

    @Column(name = "operateur_mobile_money", length = 50)
    private String operateurMobileMoney;

    @Column(name = "date_paiement")
    private LocalDateTime datePaiement;

    @Column(name = "date_validation")
    private LocalDateTime dateValidation;

    @Column(name = "url_recu", length = 500)
    private String urlRecu;

    @Column(columnDefinition = "TEXT")
    private String commentaire;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;
}
package com.app.dgpn.model.entity;


import com.app.dgpn.model.enums.TypeDemande;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "types_demande")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class TypeDemandeEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Enumerated(EnumType.STRING)
    @Column(unique = true, nullable = false, length = 50)
    private TypeDemande nom;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(name = "frais_supplementaires", precision = 10, scale = 2)
    private java.math.BigDecimal fraisSupplementaires;

    @Column(name = "ordre_affichage")
    private Integer ordreAffichage;

    @Column(nullable = false)
    private Boolean actif = true;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "typeDemande")
    private Set<PreDemande> preDemandes = new HashSet<>();

    @OneToMany(mappedBy = "typeDemande")
    private Set<DocumentRequis> documentsRequis = new HashSet<>();
}
package com.app.dgpn.model.entity;


import com.app.dgpn.model.enums.TypeDocument;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "types_document")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class TypeDocumentEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Enumerated(EnumType.STRING)
    @Column(unique = true, nullable = false, length = 50)
    private TypeDocument nom;

    @Column(columnDefinition = "TEXT")
    private String description;

    @Column(nullable = false, precision = 10, scale = 2)
    private BigDecimal tarif;

    @Column(name = "delai_traitement_jours", nullable = false)
    private Integer delaiTraitementJours;

    @Column(nullable = false)
    private Boolean actif = true;

    @Column(name = "ordre_affichage")
    private Integer ordreAffichage;

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "typeDocument")
    private Set<PreDemande> preDemandes = new HashSet<>();

    @OneToMany(mappedBy = "typeDocument")
    private Set<DocumentRequis> documentsRequis = new HashSet<>();
}

package com.app.dgpn.model.entity;

import com.app.dgpn.model.enums.StatutUtilisateur;
import com.fasterxml.jackson.annotation.JsonIgnore;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;
import java.util.stream.Collectors;

@Entity
@Table(name = "utilisateurs")
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString(exclude = {"utilisateurRoles", "preDemandes", "preDemandesCrees", "rendezVous", "notifications"})
@EqualsAndHashCode(exclude = {"utilisateurRoles", "preDemandes", "preDemandesCrees", "rendezVous", "notifications"})
public class Utilisateur {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(unique = true, nullable = false, length = 255)
    private String email;

    @Column(name = "prenom", length = 100)
    private String prenom;

    @Column(name = "nom", length = 100)
    private String nom;

    @Column(name = "mot_de_passe", nullable = false)
    private String motDePasse;

    @Column(unique = true, length = 20)
    private String telephone;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private StatutUtilisateur statut;

    @Column(name = "email_verifie", nullable = false)
    private Boolean emailVerifie = false;

    @Column(name = "telephone_verifie", nullable = false)
    private Boolean telephoneVerifie = false;

    @Column(name = "token_verification", length = 255)
    private String tokenVerification;

    @Column(name = "token_expiration")
    private LocalDateTime tokenExpiration;

    @Column(name = "token_reinitialisation", length = 255)
    private String tokenReinitialisation;

    @Column(name = "token_reinitialisation_expiration")
    private LocalDateTime tokenReinitialisationExpiration;

    @Column(name = "tentatives_connexion_echouees", nullable = false)
    private Integer tentativesConnexionEchouees = 0;

    @Column(name = "date_dernier_echec_connexion")
    private LocalDateTime dateDernierEchecConnexion;

    @Column(name = "date_blocage")
    private LocalDateTime dateBlocage;

    @Column(name = "derniere_connexion")
    private LocalDateTime derniereConnexion;

    @Column(name = "adresse_ip_derniere_connexion", length = 45)
    private String adresseIpDerniereConnexion;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "point_contact_id")
    private PointContact pointContact;

    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    @JoinColumn(name = "adresse_id")
    private Adresse adresse;

    @OneToMany(mappedBy = "utilisateur", cascade = CascadeType.ALL, fetch = FetchType.LAZY, orphanRemoval = true)
    @JsonIgnore
    private Set<UtilisateurRole> utilisateurRoles = new HashSet<>();
    
    // Méthode utilitaire pour obtenir les rôles (actifs uniquement)
    public Set<Role> getRoles() {
        if (utilisateurRoles == null) {
            return new HashSet<>();
        }
        return utilisateurRoles.stream()
                .filter(ur -> ur.getActif() != null && ur.getActif())
                .map(UtilisateurRole::getRole)
                .collect(Collectors.toSet());
    }
    
    // Méthode utilitaire pour ajouter un rôle
    public void addRole(Role role) {
        if (utilisateurRoles == null) {
            utilisateurRoles = new HashSet<>();
        }
        // Vérifier si le rôle existe déjà
        boolean existeDeja = utilisateurRoles.stream()
                .anyMatch(ur -> ur.getRole().getId().equals(role.getId()));
        if (!existeDeja) {
            UtilisateurRole utilisateurRole = new UtilisateurRole(this, role);
            utilisateurRoles.add(utilisateurRole);
        }
    }
    
    // Méthode utilitaire pour supprimer un rôle
    public void removeRole(Role role) {
        if (utilisateurRoles != null) {
            utilisateurRoles.removeIf(ur -> ur.getRole().getId().equals(role.getId()));
        }
    }
    
    // Getter pour utilisateurRoles (utilisé par JPA)
    public Set<UtilisateurRole> getUtilisateurRoles() {
        return utilisateurRoles;
    }
    
    // Setter pour utilisateurRoles (utilisé par JPA)
    public void setUtilisateurRoles(Set<UtilisateurRole> utilisateurRoles) {
        this.utilisateurRoles = utilisateurRoles;
    }

    @CreationTimestamp
    @Column(name = "date_creation", nullable = false, updatable = false)
    private LocalDateTime dateCreation;

    @CreationTimestamp
    @Column(name = "date_deblocage", nullable = false, updatable = false)
    private LocalDateTime dateDeblocage;

    @UpdateTimestamp
    @Column(name = "date_modification")
    private LocalDateTime dateModification;

    @OneToMany(mappedBy = "utilisateur")
    private Set<PreDemande> preDemandes = new HashSet<>();

    @OneToMany(mappedBy = "creePar")
    private Set<PreDemande> preDemandesCrees = new HashSet<>();

    @OneToMany(mappedBy = "utilisateur")
    private Set<RendezVous> rendezVous = new HashSet<>();

    @OneToMany(mappedBy = "utilisateur")
    private Set<Notification> notifications = new HashSet<>();
}

package com.app.dgpn.model.entity;

import com.fasterxml.jackson.annotation.JsonIgnore;
import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.ToString;
import org.hibernate.annotations.CreationTimestamp;

import java.time.LocalDateTime;
import java.util.UUID;

/**
 * Entité de liaison Many-to-Many entre Utilisateur et Role
 */
@Entity
@Table(name = "utilisateurs_roles")
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString(exclude = {"utilisateur", "role"})
@EqualsAndHashCode(exclude = {"utilisateur", "role"})
public class UtilisateurRole {

    @Id
    @GeneratedValue(strategy = GenerationType.UUID)
    private UUID id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "utilisateur_id", nullable = false)
    @JsonIgnore
    private Utilisateur utilisateur;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "role_id", nullable = false)
    private Role role;

    @Column(name = "date_attribution", nullable = false)
    @CreationTimestamp
    private LocalDateTime dateAttribution;

    @Column(name = "attribue_par")
    private UUID attribueParId; // ID de l'admin qui a attribué le rôle

    @Column(name = "actif", nullable = false)
    private Boolean actif = true;

    // Constructeur utilitaire
    public UtilisateurRole(Utilisateur utilisateur, Role role) {
        this.utilisateur = utilisateur;
        this.role = role;
        this.actif = true;
    }
}
